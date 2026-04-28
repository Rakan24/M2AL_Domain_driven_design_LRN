# Scénarios de test de domaine

---

### Invariant 1 : Une commande validée doit avoir un paiement accepté

#### Scénario 1 - Happy path
Given une commande créée avec un montant de 120€  
And un paiement associé au statut "accepté"  
When la commande passe au statut "confirmée"  
Then la transition est autorisée  
And la commande est enregistrée comme valide  

#### Scénario 2 - Sad path
Given une commande créée avec un montant de 120€  
And aucun paiement accepté n’est associé  
When la commande passe au statut "confirmée"  
Then la transition est refusée  
And une erreur métier "Paiement obligatoire avant confirmation" est retournée  

---

### Invariant 2 : Une commande livrée ne peut plus être annulée

#### Scénario 1 - Happy path
Given une commande au statut "expédiée"  
When le client demande l’annulation  
Then l’annulation est autorisée  
And le statut devient "annulée"  

#### Scénario 2 - Sad path
Given une commande au statut "livrée"  
When le client demande l’annulation  
Then l’annulation est refusée  
And une erreur métier "Commande déjà livrée" est retournée  

---

### Invariant 3 : Le montant total doit toujours être supérieur à 0

#### Scénario 1 - Happy path
Given un panier contenant 2 produits  
And le montant total est de 85€  
When la commande est créée  
Then la commande est validée  

#### Scénario 2 - Sad path
Given un panier vide  
And le montant total est de 0€  
When la commande est créée  
Then la création est refusée  
And une erreur métier "Montant invalide" est retournée  

---

### Invariant 4 : Un client doit posséder une adresse email unique

#### Scénario 1 - Happy path
Given aucun client avec l’email "client@test.com"  
When un nouveau client est créé avec cet email  
Then le client est enregistré  

#### Scénario 2 - Sad path
Given un client existant avec l’email "client@test.com"  
When un nouveau client est créé avec le même email  
Then la création est refusée  
And une erreur métier "Email déjà utilisé" est retournée  

---

### Invariant 5 : Un client actif doit avoir une adresse de livraison valide

#### Scénario 1 - Happy path
Given un client actif  
And une adresse complète est renseignée  
When le client passe une commande  
Then la commande est acceptée  

#### Scénario 2 - Sad path
Given un client actif  
And aucune adresse de livraison n’est renseignée  
When le client passe une commande  
Then la commande est refusée  
And une erreur métier "Adresse requise" est retournée  

---

### Invariant 6 : Un client suspendu ne peut pas passer de nouvelle commande

#### Scénario 1 - Happy path
Given un client au statut "actif"  
When il passe une nouvelle commande  
Then la commande est créée  

#### Scénario 2 - Sad path
Given un client au statut "suspendu"  
When il tente de passer une nouvelle commande  
Then la création est refusée  
And une erreur métier "Compte suspendu" est retournée  




