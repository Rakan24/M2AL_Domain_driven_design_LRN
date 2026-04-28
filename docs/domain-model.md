# Modèle de domaine

## Entités

| Entité | Description métier | Identifiant métier |
|---|---|---|
| Commande | Représente l’engagement formel d’achat effectué par un client. Elle contient les produits sélectionnés, l’adresse de livraison, le statut d’avancement et les informations de paiement associées. Elle constitue le cœur du flux métier car elle traverse toutes les étapes : création, validation, préparation, expédition et livraison. | CommandeId |
| Client | Représente l’utilisateur final de la plateforme e-commerce. Il peut consulter le catalogue, créer un panier, passer une commande et suivre ses livraisons. Le client possède des informations d’identité et de contact nécessaires au processus de commande. | ClientId |
| Paiement | Représente la transaction financière liée à une commande. Il permet de valider ou refuser une commande selon le résultat du paiement. Il conserve le montant, le statut et la référence de transaction fournie par le prestataire externe. | PaiementId |

---

## Objets Valeur

| Objet Valeur | Description métier | Propriétés principales |
|---|---|---|
| AdresseLivraison | Représente l’adresse complète à laquelle la commande doit être livrée. Cet objet est immuable afin de conserver l’historique exact de la commande au moment de sa validation. | rue, ville, codePostal, pays |
| Montant | Représente une valeur monétaire utilisée pour le paiement et le total de commande. Cet objet encapsule la devise et la valeur afin d’éviter les incohérences métier. | valeur, devise |
| StatutCommande | Représente l’état courant de la commande dans son cycle de vie métier. Il permet de suivre la progression du flux principal. | créée, confirmée, préparée, expédiée, livrée, annulée |

---

## Diagramme UML (conceptuel)

![Diagramme UML du modèle de domaine](./Diagramme%20UML%20domain-model.png)

Détail textuel des entités et relations :

Commande
- CommandeId
- AdresseLivraison
- StatutCommande
- Montant
- ClientId
- PaiementId

Client
- ClientId
- nom
- email
- téléphone

Paiement
- PaiementId
- Montant
- statutPaiement
- référenceTransaction

Relations :
- Un **Client** passe **0..n Commandes**
- Une **Commande** possède **1 Paiement**
- Une **Commande** contient **1 AdresseLivraison**
- Une **Commande** possède **1 StatutCommande**
- Une **Commande** possède **1 Montant**

Représentation UML simplifiée :

Client (1) -------- (0..n) Commande  
Commande (1) ------ (1) Paiement  
Commande (1) <>---- (1) AdresseLivraison  
Commande (1) <>---- (1) Montant  
Commande (1) <>---- (1) StatutCommande






