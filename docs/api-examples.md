# Exemples d’API REST

Ce document présente des exemples d’exposition REST du domaine e-commerce.
Les endpoints respectent l’Ubiquitous Language défini précédemment et permettent
d’interagir avec l’agrégat principal : Commande.

---

## Endpoint 1 — Création d’une commande

**Méthode :** POST  
**URL :** /api/commandes  

**Description :**  
Permet à un client actif de créer une nouvelle commande.  
La commande est initialisée avec un statut "créée" après validation des informations métier
(adresse de livraison, montant, client actif).

### Exemple de requête

```json
{
  "clientId": "CLI-2026-001",
  "adresseLivraison": {
    "rue": "12 rue de Rivoli",
    "ville": "Paris",
    "codePostal": "75001",
    "pays": "France"
  },
  "montant": {
    "valeur": 89.99,
    "devise": "EUR"
  }
}

```

### Exemple de réponse

```json

{
  "commandeId": "CMD-2026-1458",
  "statutCommande": "créée",
  "message": "Commande créée avec succès"
}

```




## Endpoint 2 — Lecture d’une commande

**Méthode :** GET
**URL :** /api/commandes/{commandeId}

**Description :** 
Permet de récupérer l’état actuel d’une commande ainsi que ses informations principales
(statut, montant, adresse de livraison).

Exemple d’appel

GET /api/commandes/CMD-2026-1458

### Exemple de réponse

```json

{
  "commandeId": "CMD-2026-1458",
  "clientId": "CLI-2026-001",
  "statutCommande": "expédiée",
  "montant": {
    "valeur": 89.99,
    "devise": "EUR"
  },
  "adresseLivraison": {
    "rue": "12 rue de Rivoli",
    "ville": "Paris",
    "codePostal": "75001",
    "pays": "France"
  }
}

```





