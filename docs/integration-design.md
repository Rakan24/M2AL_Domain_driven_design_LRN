# Design des intégrations inter-contextes

Ce document décrit la conception des deux intégrations principales entre Bounded Contexts : une intégration REST synchrone et une intégration par événements asynchrone.

---

## Intégration REST — ContexteCommande → ContextePaiement

### Schéma du flux

```
Client
  │
  │  POST /api/commandes
  ▼
REST Adapter (ContexteCommande)
  │
  │  Crée la commande (statut : "créée")
  ▼
Application Service — CréerCommande
  │
  │  Appel REST POST /api/paiements
  ▼
REST Adapter (ContextePaiement)
  │
  │  Sollicite le PrestatairePaiement externe
  ▼
PrestatairePaiement (externe)
  │
  │  Retourne : accepté / refusé
  ▼
REST Adapter (ContextePaiement)
  │
  │  Réponse JSON : { statut, paiementId, referenceTransaction }
  ▼
Application Service — CréerCommande
  │
  │  Si accepté → statut commande = "confirmée"
  │  Si refusé  → statut commande = "annulée"
  ▼
CommandeRepository — persist
  │
  ▼
Réponse HTTP 201 / 402 au client
```

### Narration

Lorsqu'un client valide son panier, le REST Adapter de ContexteCommande reçoit la requête HTTP et la transmet au service applicatif `CréerCommande`. Ce service crée l'entité `Commande` avec le statut `créée` et déclenche immédiatement une demande de paiement vers ContextePaiement via un appel REST synchrone.

ContextePaiement reçoit la demande, l'enrichit avec les informations de transaction, puis contacte le prestataire externe. La réponse (acceptée ou refusée) est retournée en JSON à ContexteCommande. Si le paiement est accepté, la commande passe au statut `confirmée` et est persistée. Si le paiement est refusé, la commande est annulée et le stock n'est pas réservé.

Cette intégration est synchrone et bloquante : le client attend la réponse complète avant que le système ne lui confirme la commande. Ce choix est justifié par le fait que la validation du paiement est une étape critique qui conditionne toute la suite du flux.

---

## Intégration par événements — ContexteOrchestration → ContexteLogistique

### Schéma du flux

```
ContexteOrchestration
  │
  │  Publie l'événement "CommandeConfirmee"
  ▼
Broker de messages (ex : RabbitMQ / Kafka)
  │
  │  Topic : commandes.confirmees
  ▼
ContexteLogistique (consommateur)
  │
  │  Déclenche la préparation de commande
  ▼
Application Service — PréparerCommande
  │
  │  Assigne un préparateur
  │  Réserve les articles en stock
  ▼
LogistiqueRepository — persist
  │
  │  Publie l'événement "CommandePreparee"
  ▼
Broker de messages
  │
  ├──► ContexteLivraison (assignation transporteur)
  └──► ContexteNotification (email client : "commande en préparation")
```

### Narration

Une fois la commande confirmée par ContexteCommande, ContexteOrchestration publie un événement `CommandeConfirmee` sur le broker de messages. Cet événement contient l'ensemble des informations nécessaires à la préparation : identifiant de commande, adresse de livraison, lignes de commande et montant total.

ContexteLogistique est abonné au topic `commandes.confirmees`. Dès réception de l'événement, son service applicatif `PréparerCommande` est déclenché : un préparateur est assigné, les articles sont réservés dans le stock et le processus de préparation physique est lancé.

À l'issue de la préparation, ContexteLogistique publie à son tour un événement `CommandePreparee`. Cet événement est consommé par ContexteLivraison pour déclencher l'assignation d'un transporteur, et par ContexteNotification pour informer le client de l'avancement de sa commande.

Cette approche asynchrone découple ContexteOrchestration de ContexteLogistique : l'orchestration n'attend pas la fin de la préparation pour continuer. Elle garantit une meilleure résilience et permet une scalabilité indépendante des deux contextes.
