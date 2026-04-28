# Scénario complet inter-contextes

## Description narrative

Le scénario suivant couvre le flux complet d'une commande e-commerce, depuis la validation du panier par le client jusqu'à la livraison finale, en impliquant l'ensemble des Bounded Contexts du système.

Marie, cliente active sur la plateforme, a sélectionné deux articles dans son panier pour un montant total de 89,99 €. Elle renseigne son adresse de livraison (12 rue de Rivoli, Paris) et clique sur **Passer commande**.

Le front-end envoie une requête `POST /api/commandes` au REST Adapter de **ContexteCommande**. Le service applicatif `CréerCommande` vérifie que Marie est une cliente active, que son adresse de livraison est valide et que le montant est supérieur à zéro (invariants de l'AgrégatCommande). La commande est créée avec l'identifiant `CMD-2026-1458` et le statut `créée`.

**ContexteCommande** appelle immédiatement **ContextePaiement** via REST pour valider la transaction. Le prestataire bancaire externe confirme le paiement : la transaction `PAY-2026-0987` est acceptée. La commande passe au statut `confirmée` et est persistée.

**ContexteOrchestration**, informé via un événement interne `CommandeConfirmee`, publie cet événement sur le broker de messages. Trois contextes le consomment simultanément :

- **ContexteLogistique** déclenche la préparation : un préparateur est assigné, les deux articles sont prélevés en stock et le colis est emballé. Une fois la préparation terminée, l'événement `CommandePreparee` est publié.
- **ContexteNotification** envoie un email à Marie : "Votre commande CMD-2026-1458 a été confirmée."
- **ContexteFacturation** génère la facture correspondante au paiement validé.

Suite à l'événement `CommandePreparee`, **ContexteLivraison** assigne le colis à un transporteur partenaire. Le transporteur prend en charge la livraison et met à jour le statut de suivi en temps réel. L'événement `CommandeExpediee` est publié.

**ContexteNotification** informe à nouveau Marie : "Votre colis est en route." Deux jours plus tard, le transporteur confirme la livraison. L'événement `CommandeLivree` est publié. Le statut de la commande passe définitivement à `livrée`. Marie reçoit une dernière notification de confirmation.

---

## Liste des événements déclenchés (dans l'ordre)

1. `CommandeCréée` — ContexteCommande, après création de l'entité Commande
2. `PaiementInitié` — ContextePaiement, lors de la demande de transaction
3. `PaiementAccepté` — ContextePaiement, après confirmation du prestataire externe
4. `CommandeConfirmée` — ContexteOrchestration, après acceptation du paiement
5. `FactureGénérée` — ContexteFacturation, déclenchée par la confirmation
6. `NotificationEnvoyée (confirmation)` — ContexteNotification, email de confirmation
7. `PreparationDéclenchée` — ContexteLogistique, assignation du préparateur
8. `StockRéservé` — ContexteStock, réservation des articles
9. `CommandePréparée` — ContexteLogistique, colis prêt à l'expédition
10. `NotificationEnvoyée (préparation)` — ContexteNotification, mise à jour client
11. `TransporteurAssigné` — ContexteLivraison, prise en charge du colis
12. `CommandeExpédiée` — ContexteLivraison, remise au transporteur
13. `NotificationEnvoyée (expédition)` — ContexteNotification, email "en route"
14. `CommandeLivrée` — ContexteLivraison, confirmation de remise au client
15. `NotificationEnvoyée (livraison)` — ContexteNotification, email final

---

## Rappel des invariants concernés

| Invariant | Agrégat | Étape concernée |
|---|---|---|
| Une commande validée doit avoir un paiement accepté | AgrégatCommande | Étape 3 → 4 : la commande ne passe à `confirmée` qu'après `PaiementAccepté` |
| Le montant total doit toujours être supérieur à 0 | AgrégatCommande | Étape 1 : vérification avant création de la commande |
| Une commande livrée ne peut plus être annulée | AgrégatCommande | Étape 14 : le statut `livrée` est terminal, toute annulation passe par un retour |
| Un client actif doit avoir une adresse de livraison valide | AgrégatClient | Étape 1 : vérification avant création de la commande |
| Un client suspendu ne peut pas passer de nouvelle commande | AgrégatClient | Étape 1 : vérification du statut client au démarrage du flux |

---

## Contextes impliqués

| Bounded Context | Rôle dans le scénario |
|---|---|
| ContexteCommande | Création et gestion du cycle de vie de la commande |
| ContextePaiement | Validation de la transaction financière |
| ContexteOrchestration | Coordination inter-contextes, publication des événements |
| ContexteStock | Réservation des articles commandés |
| ContexteLogistique | Préparation physique et expédition du colis |
| ContexteLivraison | Suivi et confirmation de la livraison finale |
| ContexteNotification | Information du client à chaque étape clé |
| ContexteFacturation | Génération de la facture après paiement |
