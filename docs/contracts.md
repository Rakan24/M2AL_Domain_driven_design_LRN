# Contrats d'échange entre contexts

Ce document formalise les contrats d'échange définis entre les Bounded Contexts du système e-commerce.
Deux contrats sont détaillés : un contrat REST entre ContexteCommande et ContextePaiement, et un contrat événementiel entre ContexteOrchestration et ContexteLogistique.

---

## Contrat 1 — ContexteCommande → ContextePaiement

**Context source :** ContexteCommande  
**Context cible :** ContextePaiement  
**Type d'échange :** REST (requête/réponse synchrone)  
**Cas d'usage :** Lors de la validation d'une commande, ContexteCommande envoie une demande de paiement à ContextePaiement. Ce dernier retourne le résultat de la transaction (acceptée ou refusée).

### Schéma de la requête (JSON)

```json
{
  "commandeId": "CMD-2026-1458",
  "clientId": "CLI-2026-001",
  "montant": {
    "valeur": 89.99,
    "devise": "EUR"
  },
  "moyenPaiement": {
    "type": "carte_bancaire",
    "tokenPrestataire": "tok_visa_xxxx1234"
  }
}
```

### Schéma de la réponse (JSON)

```json
{
  "paiementId": "PAY-2026-0987",
  "commandeId": "CMD-2026-1458",
  "statut": "accepté",
  "referenceTransaction": "TXN-EXT-55678",
  "montantDebite": {
    "valeur": 89.99,
    "devise": "EUR"
  },
  "datePaiement": "2026-04-01T14:35:00Z"
}
```

### Schéma de réponse en cas d'erreur (JSON)

```json
{
  "paiementId": null,
  "commandeId": "CMD-2026-1458",
  "statut": "refusé",
  "motifRefus": "Fonds insuffisants",
  "datePaiement": "2026-04-01T14:35:01Z"
}
```

---

## Contrat 2 — ContexteOrchestration → ContexteLogistique

**Context source :** ContexteOrchestration  
**Context cible :** ContexteLogistique  
**Type d'échange :** Événement (publish/subscribe via broker de messages)  
**Cas d'usage :** Après confirmation du paiement, ContexteOrchestration publie un événement `CommandeConfirmée` sur le broker. ContexteLogistique s'y abonne pour déclencher la préparation de la commande.

### Schéma de l'événement publié (JSON)

```json
{
  "evenement": "CommandeConfirmee",
  "version": "1.0",
  "timestamp": "2026-04-01T14:36:00Z",
  "payload": {
    "commandeId": "CMD-2026-1458",
    "clientId": "CLI-2026-001",
    "adresseLivraison": {
      "rue": "12 rue de Rivoli",
      "ville": "Paris",
      "codePostal": "75001",
      "pays": "France"
    },
    "lignesCommande": [
      {
        "produitId": "PRD-00456",
        "quantite": 2,
        "montantUnitaire": {
          "valeur": 44.99,
          "devise": "EUR"
        }
      }
    ],
    "montantTotal": {
      "valeur": 89.99,
      "devise": "EUR"
    }
  }
}
```

### Schéma de l'accusé de réception (JSON)

```json
{
  "evenement": "PreparationCommandeDeclenchee",
  "version": "1.0",
  "timestamp": "2026-04-01T14:36:05Z",
  "payload": {
    "commandeId": "CMD-2026-1458",
    "statutLogistique": "en_preparation",
    "preparateurAssigne": "PREP-007"
  }
}
```
