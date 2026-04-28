# Observabilité

Ce document décrit la stratégie d'observabilité du système e-commerce : corrélation des traces distribuées, métriques métier exposées et format de logging structuré.

---

## Correlation ID

Le Correlation ID est un identifiant unique généré à l'entrée du système, dès la réception d'une requête externe par le REST Adapter. Il est créé sous la forme d'un UUID (ex : `corr-550e8400-e29b-41d4-a716-446655440000`) et injecté dans le contexte de chaque opération.

Il est propagé à travers toutes les couches du système : de l'Adapter vers l'Application Service, puis vers le Domain, les Repository Adapters et les Messaging Adapters. Lorsque ContexteCommande publie un événement vers le broker, le Correlation ID est inclus dans les headers du message afin que les contextes consommateurs (Logistique, Notification, Livraison) puissent le reprendre et le propager à leur tour.

Chaque ligne de log produite par le système inclut ce Correlation ID. Cela permet de reconstituer l'ensemble du parcours d'une requête ou d'un événement à travers les différents Bounded Contexts, même dans un environnement distribué. Les outils de monitoring (Grafana, Loki, Jaeger) peuvent filtrer les traces par Correlation ID pour diagnostiquer rapidement un incident.

---

## Métriques métier

| Nom de la métrique | Description | Type |
|---|---|---|
| `commandes_creees_total` | Nombre total de commandes créées depuis le démarrage du service. Permet de mesurer le volume d'activité et de détecter des anomalies de flux (baisse soudaine, pic inhabituel). | counter |
| `paiements_refuses_total` | Nombre total de paiements refusés par le prestataire externe. Un taux élevé peut signaler un problème technique ou une fraude en cours. | counter |
| `duree_preparation_commande_secondes` | Temps écoulé entre la réception d'une commande par ContexteLogistique et la publication de l'événement `CommandePreparee`. Permet de surveiller la performance opérationnelle des préparateurs. | histogram |
| `commandes_en_cours` | Nombre de commandes actuellement dans un état intermédiaire (créée, confirmée, en préparation, expédiée). Reflète la charge instantanée du système. | gauge |
| `livraisons_en_retard_total` | Nombre de livraisons dont le délai estimé est dépassé. Indicateur de qualité de service pour les partenaires transporteurs. | counter |

---

## Logging structuré

Chaque service du système produit des logs au format JSON. Ce format facilite l'ingestion dans des outils comme Loki, Elasticsearch ou Datadog.

### Exemple de log — création d'une commande

```json
{
  "timestamp": "2026-04-01T14:35:00.123Z",
  "level": "INFO",
  "service": "contexte-commande",
  "correlationId": "corr-550e8400-e29b-41d4-a716-446655440000",
  "commandeId": "CMD-2026-1458",
  "clientId": "CLI-2026-001",
  "action": "CréerCommande",
  "statut": "créée",
  "montant": 89.99,
  "devise": "EUR",
  "message": "Commande créée avec succès"
}
```

### Exemple de log — paiement refusé

```json
{
  "timestamp": "2026-04-01T14:35:01.456Z",
  "level": "WARN",
  "service": "contexte-paiement",
  "correlationId": "corr-550e8400-e29b-41d4-a716-446655440000",
  "commandeId": "CMD-2026-1458",
  "paiementId": null,
  "action": "ValiderPaiement",
  "statut": "refusé",
  "motifRefus": "Fonds insuffisants",
  "prestataire": "PrestataireBancaireExterne",
  "message": "Paiement refusé par le prestataire"
}
```

### Exemple de log — erreur technique

```json
{
  "timestamp": "2026-04-01T14:36:10.789Z",
  "level": "ERROR",
  "service": "contexte-logistique",
  "correlationId": "corr-550e8400-e29b-41d4-a716-446655440000",
  "commandeId": "CMD-2026-1458",
  "action": "PréparerCommande",
  "erreur": "StockInsuffisantException",
  "message": "Stock insuffisant pour le produit PRD-00456 : 0 unité disponible"
}
```
