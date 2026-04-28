# Entités et Objets Valeur — Vue conceptuelle

## Entités

### Commande
| Attribut | Type métier | Description |
|----------|------------|------------|
| CommandeId | Identifiant métier unique | Identifie de manière unique la commande dans le système. |
| Client | Acteur | Le client qui a passé la commande. |
| Produits | Liste de produits | Liste des produits inclus dans la commande avec quantité et prix. |
| StatutCommande | État métier | Représente l’état actuel de la commande (Créée, Confirmée, Préparée, Expédiée, Livrée). |
| DateCreation | Date | Date à laquelle la commande a été créée. |

**Invariants :**  
- Une commande doit contenir au moins un produit.
- Une commande doit avoir un client associé.
- Le statut de la commande doit respecter la séquence logique : Créée → Confirmée → Préparée → Expédiée → Livrée.



### Paiement
| Attribut | Type métier | Description |
|----------|------------|------------|
| PaiementId | Identifiant métier unique | Identifie de manière unique le paiement associé à une commande. |
| CommandeId | Référence | Commande associée au paiement. |
| Montant | Montant | Montant total payé par le client. |
| StatutPaiement | État métier | Indique si le paiement est validé, refusé ou en attente. |
| DatePaiement | Date | Date de réalisation du paiement. |

**Invariants :**  
- Le montant du paiement doit correspondre au total de la commande associée.  
- Chaque paiement doit être associé à exactement une commande.  
- Le statut de paiement ne peut être valide que si le montant est positif et la commande existe.






## Objet Valeur

### AdresseLivraison
| Propriétés | Description |
|------------|------------|
| Rue | Nom de la rue ou voie |
| Ville | Ville de livraison |
| CodePostal | Code postal |
| Pays | Pays de livraison |

**Explication de l’immuabilité :**  
AdresseLivraison est immuable car elle représente une valeur exacte qui ne doit pas changer une fois associée à une commande. Modifier une adresse exigerait de créer une nouvelle instance plutôt que d’altérer l’ancienne, garantissant ainsi la cohérence historique des commandes et la traçabilité. Chaque propriété est fixée lors de la création de l’objet et ne peut pas être modifiée.




