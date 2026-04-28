# Architecture runtime

## Description des services

L'architecture runtime du système e-commerce repose sur un ensemble de services orchestrés via Docker Compose. Chaque service métier correspond à un Bounded Context et dispose de sa propre base de données (principe de ségrégation des données). Les services communiquent soit via des appels REST synchrones, soit via le broker de messages RabbitMQ pour les échanges asynchrones.

**commande-service** est le cœur du système. Il expose une API REST sur le port 8081 et dépend de sa base de données `commande-db` ainsi que du service `paiement-service` pour la validation des transactions. Il publie des événements vers RabbitMQ à destination des autres contextes.

**paiement-service** gère les transactions financières. Il communique avec le prestataire bancaire externe et retourne le résultat à `commande-service`. Il publie un événement vers `facturation-service` après chaque paiement accepté.

**orchestration-service** est un service stateless qui consomme les événements de `commande-service` et pilote le séquençage des actions logistiques. Il orchestre les transitions d'état inter-contextes sans logique métier propre.

**logistique-service** et **livraison-service** sont abonnés aux événements de `orchestration-service`. Ils gèrent respectivement la préparation des colis et leur acheminement par les transporteurs.

**stock-service** est consulté en lecture par `commande-service` et `logistique-service` pour vérifier la disponibilité des produits avant création et préparation de commande.

**notification-service** et **facturation-service** sont des services génériques abonnés aux événements clés du système. Ils sont stateless et ne font que réagir aux événements reçus.

**RabbitMQ** est le broker de messages central. Il gère les queues d'événements entre les services. L'interface de management est exposée sur le port 15672.

**Prometheus** scrape les métriques exposées par chaque service sur `/metrics`. **Grafana** se connecte à Prometheus pour afficher les dashboards de monitoring.

---

## Schéma des services et leurs liens

```
                        ┌─────────────────────────────────────────────────┐
                        │              RÉSEAU DOCKER INTERNE              │
                        │                                                 │
  Client Web            │   ┌──────────────────┐                          │
     │                  │   │  commande-service │◄──── REST ─────────────►│ paiement-service
     │ HTTP :8081       │   │  :8081            │                         │  :8082
     └──────────────────►   │  ──────────────── │                         │     │
                        │   │  commande-db      │                         │  paiement-db
                        │   └────────┬──────────┘                         │     │
                        │            │ publie événements                  │     │ publie événements
                        │            ▼                                    │     ▼
                        │   ┌──────────────────────────────────────────┐  │  facturation-service
                        │   │              RabbitMQ :5672              │  │  :8088
                        │   │         (broker de messages)             │  │     │
                        │   └──┬───────────┬──────────────┬────────────┘  │  facturation-db
                        │      │           │              │               │
                        │      ▼           ▼              ▼               │
                        │  orchestration  notification  stock-service     │
                        │  -service       -service      :8086             │
                        │  :8083          :8087          stock-db         │
                        │      │                                          │
                        │      ├──► logistique-service :8084              │
                        │      │    logistique-db                         │
                        │      │                                          │
                        │      └──► livraison-service :8085               │
                        │           livraison-db                          │
                        │                                                 │
                        │   ┌─────────────┐    ┌──────────────────────┐   │
                        │   │  Prometheus │    │       Grafana        │   │
                        │   │  :9090      │◄───│       :3000          │   │
                        │   │  (scrape    │    │  (dashboards)        │   │
                        │   │  /metrics)  │    └──────────────────────┘   │
                        │   └─────────────┘                               │ 
                        └─────────────────────────────────────────────────┘
```

---

## Extrait de docker-compose.yml (conceptuel)

```yaml
version: "3.9"

services:

  commande-service:
    image: ecommerce/commande-service:latest
    ports:
      - "8081:8081"
    environment:
      - DB_HOST=commande-db
      - BROKER_URL=amqp://rabbitmq:5672
      - PAYMENT_SERVICE_URL=http://paiement-service:8082
    depends_on:
      - commande-db
      - rabbitmq

  commande-db:
    image: postgres:15
    environment:
      - POSTGRES_DB=commande
      - POSTGRES_USER=commande_user
      - POSTGRES_PASSWORD=secret

  paiement-service:
    image: ecommerce/paiement-service:latest
    ports:
      - "8082:8082"
    environment:
      - DB_HOST=paiement-db
      - BROKER_URL=amqp://rabbitmq:5672
    depends_on:
      - paiement-db
      - rabbitmq

  paiement-db:
    image: postgres:15

  orchestration-service:
    image: ecommerce/orchestration-service:latest
    ports:
      - "8083:8083"
    environment:
      - BROKER_URL=amqp://rabbitmq:5672
    depends_on:
      - rabbitmq

  logistique-service:
    image: ecommerce/logistique-service:latest
    ports:
      - "8084:8084"
    depends_on:
      - logistique-db
      - rabbitmq

  logistique-db:
    image: postgres:15

  livraison-service:
    image: ecommerce/livraison-service:latest
    ports:
      - "8085:8085"
    depends_on:
      - livraison-db
      - rabbitmq

  livraison-db:
    image: postgres:15

  stock-service:
    image: ecommerce/stock-service:latest
    ports:
      - "8086:8086"
    depends_on:
      - stock-db

  stock-db:
    image: postgres:15

  notification-service:
    image: ecommerce/notification-service:latest
    ports:
      - "8087:8087"
    depends_on:
      - rabbitmq

  facturation-service:
    image: ecommerce/facturation-service:latest
    ports:
      - "8088:8088"
    depends_on:
      - facturation-db
      - rabbitmq

  facturation-db:
    image: postgres:15

  rabbitmq:
    image: rabbitmq:3-management
    ports:
      - "5672:5672"
      - "15672:15672"

  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9090:9090"

  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    depends_on:
      - prometheus
```
