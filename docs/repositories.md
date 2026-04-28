# Repositories métier

---

## CommandeRepository

| Opération métier | Description | Contraintes / règles métier |
|---|---|---|
| CreateCommande | Permet d’enregistrer une nouvelle commande après validation du panier, du client et du paiement. La commande est créée avec un identifiant métier unique `CommandeId` et un statut initial `créée`. Cette opération est utilisée lors du passage de commande. | Le montant doit être strictement supérieur à 0. Le client doit être actif. Une commande ne peut être créée qu’avec une adresse de livraison valide. |
| ReadCommandeById | Permet de retrouver une commande à partir de son `CommandeId`. Utilisé pour le suivi de commande, l’annulation ou la consultation par le service client. | L’identifiant doit exister. Une commande inexistante doit retourner une erreur métier `CommandeIntrouvable`. |
| UpdateStatutCommande | Permet de mettre à jour l’état d’avancement d’une commande (`confirmée`, `préparée`, `expédiée`, `livrée`, `annulée`). Utilisé par les services de préparation et livraison. | Les transitions doivent respecter le cycle métier. Exemple : une commande `livrée` ne peut pas redevenir `créée`. |
| DeleteCommande | Suppression logique d’une commande annulée ou créée par erreur avant validation définitive. | Interdit si la commande est déjà expédiée ou livrée. La suppression doit conserver une traçabilité métier. |

---

## ClientRepository

| Opération métier | Description | Contraintes / règles métier |
|---|---|---|
| CreateClient | Enregistre un nouveau client dans le système avec un identifiant unique `ClientId`. Cette opération est utilisée lors de la création de compte. | L’email doit être unique. Les informations minimales doivent être présentes : nom, email, adresse. |
| ReadClientById | Permet de récupérer les informations d’un client à partir de son `ClientId`. Utilisé pour l’authentification, le suivi de commandes et le support client. | Le client doit exister dans le système. |
| UpdateClient | Permet de modifier les informations du client, notamment son adresse de livraison ou ses coordonnées. | L’adresse email doit rester unique. Les modifications doivent être historisées si elles impactent les commandes en cours. |
| SuspendClient | Permet de changer le statut du client en `suspendu` suite à une fraude, un impayé ou un litige. | Un client suspendu ne peut plus créer de nouvelle commande. |





