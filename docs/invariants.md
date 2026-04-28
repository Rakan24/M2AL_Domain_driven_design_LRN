# Agrégats et invariants

---

## Agrégat 1 : AgrégatCommande

### Racine de l’agrégat
Commande

### Liste des entités / objets valeur internes
- Commande (racine)
- Paiement
- AdresseLivraison
- Montant
- StatutCommande

### Tableau des invariants

| Invariant | Description métier | Conséquence si non respecté |
|---|---|---|
| Une commande validée doit avoir un paiement accepté | Dès qu’une commande passe au statut `confirmée`, un paiement associé doit obligatoirement exister avec le statut `accepté`. Cela garantit qu’aucune commande n’entre dans le circuit logistique sans règlement. | Risque d’expédier une commande non payée, perte financière pour l’entreprise |
| Une commande livrée ne peut plus être annulée | Une fois le statut `livrée`, le système ne doit plus permettre une annulation métier directe. Le flux doit alors passer par un processus de retour ou remboursement distinct. | Incohérence métier et historique erroné des commandes |
| Le montant total doit toujours être supérieur à 0 | Une commande valide doit toujours contenir au moins un produit et avoir un montant strictement positif. | Commandes vides ou invalides enregistrées dans le système |

---

## Agrégat 2 : AgrégatClient

### Racine de l’agrégat
Client

### Liste des entités / objets valeur internes
- Client (racine)
- AdresseLivraison préférée
- HistoriqueCommandes (référence métier)

### Tableau des invariants

| Invariant | Description métier | Conséquence si non respecté |
|---|---|---|
| Un client doit posséder une adresse email unique | L’adresse email sert d’identifiant fonctionnel pour la connexion, les notifications et le suivi de commande. Deux clients ne peuvent pas partager la même adresse. | Risque de confusion d’identité et d’erreurs de notification |
| Un client actif doit avoir au moins une adresse de livraison valide | Pour passer commande, le client doit posséder une adresse exploitable par le système logistique. | Impossibilité de livrer les commandes |
| Un client suspendu ne peut pas passer de nouvelle commande | Si le compte est suspendu (fraude, litige, impayé), aucune nouvelle commande ne peut être créée. | Risque financier ou sécurité métier compromise |

---

## Schéma UML des agrégats

```
┌──────────────────────────────────────────────┐
│             <<Agrégat>>                      │
│           AgrégatCommande                    │
│                                              │
│  ┌───────────────────────────────────────┐   │
│  │  Commande  (racine)                   │   │
│  │  - CommandeId : identifiant métier    │   │
│  │  - StatutCommande : état              │   │
│  │  - Montant : objet valeur             │   │
│  │  - AdresseLivraison : objet valeur    │   │
│  └──────────────────┬────────────────────┘   │
│                     │ contient               │
│          ┌──────────┴─────────┐              │
│          │      Paiement      │              │
│          │  - PaiementId      │              │
│          │  - Montant         │              │
│          │  - StatutPaiement  │              │
│          └────────────────────┘              │
└──────────────────────────────────────────────┘

┌──────────────────────────────────────────────┐
│             <<Agrégat>>                      │
│            AgrégatClient                     │
│                                              │
│  ┌───────────────────────────────────────┐   │
│  │  Client  (racine)                     │   │
│  │  - ClientId : identifiant métier      │   │
│  │  - email : unique                     │   │
│  │  - statut : actif / suspendu          │   │
│  └──────────────────┬────────────────────┘   │
│                     │ possède                │
│    ┌────────────────┴──────────────────┐     │
│    │       AdresseLivraison (OV)       │     │
│    │  - rue, ville, codePostal, pays   │     │
│    └───────────────────────────────────┘     │
└──────────────────────────────────────────────┘
```

**Légende :**
- Les agrégats sont délimités par les boîtes extérieures.
- La racine de chaque agrégat est clairement identifiée.
- Les objets valeur (OV) sont immuables et contenus dans l'agrégat.
- `AgrégatCommande` référence `AgrégatClient` via `ClientId` uniquement (pas d'inclusion directe).




