# Architecture hexagonale

## Description des couches

### Couche Domain
La couche **Domain** contient toute la logique métier centrale du système e-commerce.  
Elle regroupe les **entités** (`Commande`, `Client`, `Paiement`), les **objets valeur** (`Montant`, `AdresseLivraison`, `StatutCommande`) ainsi que les **règles métier et invariants** définis précédemment.  

Cette couche est totalement indépendante de la technique : elle ne connaît ni base de données, ni API REST, ni broker de messages.  
Son rôle est de garantir la cohérence du métier, par exemple :  
- empêcher la validation d’une commande sans paiement accepté  
- interdire l’annulation d’une commande déjà livrée  
- garantir qu’un client suspendu ne puisse pas commander  

---

### Couche Application
La couche **Application** orchestre les cas d’usage métier.  
Elle agit comme intermédiaire entre les entrées externes (API, messages) et le domaine.

Elle contient les services applicatifs qui pilotent les actions telles que :
- `CréerCommande`
- `ValiderPaiement`
- `AnnulerCommande`
- `SuivreLivraison`

Cette couche appelle les entités du domaine, applique les cas d’usage et coordonne les accès aux repositories.

Exemple :  
lorsqu’un client passe commande, le service applicatif :
1. vérifie le client
2. crée la commande
3. déclenche le paiement
4. met à jour le statut

---

### Couche Adapters
La couche **Adapters** correspond à toutes les interfaces avec l’extérieur.

Elle comprend notamment :
- **REST Adapter** → reçoit les requêtes HTTP
- **Persistence Adapter** → dialogue avec la base de données
- **Messaging Adapter** → publie ou consomme les événements
- **External Payment Adapter** → échange avec le prestataire de paiement

Cette couche traduit les données techniques vers le langage métier du domaine.

Exemple :  
une requête JSON reçue via REST est transformée en commande métier `CréerCommande`.

---

## Exemple de flux (commande → réponse)

Exemple concret : création d’une commande

1. Le client envoie une requête HTTP `POST /commandes`
2. Le **REST Adapter** reçoit la requête JSON
3. Il transmet les données au service applicatif `CréerCommande`
4. La couche **Application** orchestre le cas d’usage
5. Le **Domain** vérifie les invariants métier
6. La commande est créée avec le statut `créée`
7. Le **Persistence Adapter** sauvegarde la commande
8. Un événement `CommandeCréée` peut être publié
9. La réponse HTTP `201 Created` est renvoyée au client

---

## Schéma simplifié

Client
   ↓
REST Adapter
   ↓
Application Service
   ↓
Domain
   ↓
Repository Adapter / Event Adapter
   ↓
Base de données / Broker




