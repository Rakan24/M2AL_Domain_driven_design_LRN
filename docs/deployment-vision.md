# Vision de déploiement

## Description

Le système e-commerce est décomposé en services indépendants, chacun correspondant à un Bounded Context identifié lors de la modélisation DDD. Chaque service est conteneurisé dans une image Docker distincte, ce qui garantit l'isolation, la portabilité et la scalabilité indépendante de chaque composant.

Les services conteneurisés sont : `commande-service`, `paiement-service`, `orchestration-service`, `logistique-service`, `livraison-service`, `stock-service`, `notification-service` et `facturation-service`. À ces services métier s'ajoutent les services d'infrastructure : une base de données PostgreSQL par contexte (principe de base de données privée par service), un broker de messages RabbitMQ pour les échanges asynchrones, et une stack d'observabilité (Prometheus, Grafana, Loki).

La configuration de chaque service est injectée via des variables d'environnement au démarrage du conteneur. Les secrets (clés API du prestataire de paiement, mots de passe de bases de données) sont gérés via des fichiers `.env` ou un gestionnaire de secrets externe (type Vault) en environnement de production.

Chaque image Docker est construite à partir d'une image de base légère. Le Dockerfile expose uniquement le port applicatif nécessaire et déclare un health check pour permettre à l'orchestrateur de surveiller la santé du service.

---

## Exemple annoté de Dockerfile — commande-service

```dockerfile
# Étape 1 : image de base légère
FROM base-image:slim

# Étape 2 : définir le répertoire de travail dans le conteneur
WORKDIR /app

# Étape 3 : copier les fichiers de configuration et de dépendances
COPY config/ ./config/
COPY src/ ./src/

# Étape 4 : installer les dépendances du service
RUN install-dependencies

# Étape 5 : exposer le port applicatif du service
EXPOSE 8081

# Étape 6 : déclarer les variables d'environnement nécessaires
ENV DB_HOST=commande-db \
    DB_PORT=5432 \
    BROKER_URL=amqp://rabbitmq:5672 \
    PAYMENT_SERVICE_URL=http://paiement-service:8082

# Étape 7 : définir un health check pour la supervision
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD curl -f http://localhost:8081/health || exit 1

# Étape 8 : commande de démarrage du service
CMD ["start-service"]
```

> **Note :** Ce Dockerfile est un pseudo-code non fonctionnel illustrant les principes de conteneurisation. Les commandes réelles dépendent du langage et du runtime choisi lors de l'implémentation.

---

## Services et ports associés

| Service | Port exposé | Base de données |
|---|---|---|
| commande-service | 8081 | commande-db (PostgreSQL) |
| paiement-service | 8082 | paiement-db (PostgreSQL) |
| orchestration-service | 8083 | — (stateless) |
| logistique-service | 8084 | logistique-db (PostgreSQL) |
| livraison-service | 8085 | livraison-db (PostgreSQL) |
| stock-service | 8086 | stock-db (PostgreSQL) |
| notification-service | 8087 | — (stateless) |
| facturation-service | 8088 | facturation-db (PostgreSQL) |
| rabbitmq | 5672 / 15672 | — |
| prometheus | 9090 | — |
| grafana | 3000 | — |
