# Scénarios d’intégration

## Scénario end-to-end : Création et validation d’une commande

Le scénario suivant illustre le parcours complet d’une commande,
depuis la réception d’une requête externe jusqu’à la mise à jour du domaine.

1. Un client connecté sur la plateforme e-commerce valide son panier
   et clique sur le bouton **Passer commande**.

2. Le front-end envoie une requête HTTP `POST /api/commandes`
   au **REST Adapter**.

3. Le REST Adapter transforme le JSON reçu en commande métier
   `CréerCommande`.

4. Le service applicatif de la couche **Application**
   reçoit la commande et vérifie :
   - l’existence du client
   - le statut actif du client
   - la validité de l’adresse de livraison
   - le montant total

5. La couche **Domain** crée une nouvelle entité `Commande`
   avec :
   - `CommandeId`
   - `StatutCommande = créée`
   - `Montant`
   - `AdresseLivraison`

6. Les invariants métier sont vérifiés :
   - montant > 0
   - client actif
   - adresse valide

7. Le **CommandeRepository**
   persiste l’agrégat `Commande`.

8. Un événement métier `CommandeCréée`
   est publié vers le broker de messages.

9. Le service Paiement consomme l’événement
   et initie la transaction.

10. Une fois le paiement accepté,
    le statut de la commande passe à `confirmée`.

11. Le système retourne une réponse HTTP `201 Created`
    contenant :
    - `commandeId`
    - `statutCommande`

12. Le client reçoit la confirmation de commande.

---

## Schéma de séquence (à reproduire sur Draw.io)

Client → API REST → Application Service → Domain → Repository → Broker → Réponse client

Séquence détaillée :

Client
   ↓ POST /api/commandes
REST Adapter
   ↓
Application Service
   ↓
Domain (création Commande)
   ↓
CommandeRepository
   ↓
Publication événement CommandeCréée
   ↓
Réponse HTTP 201




