# Ubiquitous Language — Version 1

| Terme | Définition métier | Exemple concret |
|------|------------------|----------------|
| Client | Personne physique qui utilise la plateforme e-commerce pour acheter des produits. Il peut consulter le catalogue, créer un panier, passer une commande et suivre ses livraisons. Le client est au centre du flux métier. | Un utilisateur crée un compte et commande un ordinateur portable via la plateforme. |
| Produit | Bien physique proposé à la vente sur la plateforme. Il est identifié par ses caractéristiques (nom, description, prix, disponibilité). Un produit peut être commandé, stocké et livré. | Un smartphone affiché dans le catalogue avec son prix et sa disponibilité. |
| Catalogue | Ensemble structuré des produits disponibles à la vente. Il permet la navigation, la recherche et la consultation des produits. Le catalogue est la vitrine commerciale du système. | La page listant toutes les catégories et produits disponibles. |
| Panier | Conteneur temporaire dans lequel le client ajoute des produits avant de passer commande. Il représente une intention d’achat non encore validée. Le panier peut être modifié à tout moment. | Le client ajoute deux produits et voit leur récapitulatif dans son panier. |
| Commande | Engagement formel du client d’acheter un ensemble de produits. Elle est créée après validation du panier et paiement. La commande est suivie jusqu’à la livraison. | Une commande confirmée avec un numéro unique après paiement. |
| CommandeId | Identifiant métier unique d’une commande dans le système. Il permet de tracer l’ensemble du cycle de vie de la commande. | CMD-2026-000154 |
| Paiement | Acte par lequel le client règle le montant de sa commande via un prestataire externe. Il conditionne la validation de la commande. | Paiement par carte bancaire validé via un service de paiement. |
| TransactionPaiement | Représentation métier de l’opération financière liée à un paiement. Elle contient les informations de statut, montant et référence externe. | Transaction validée avec un identifiant du prestataire bancaire. |
| Stock | Quantité disponible d’un produit dans le système logistique. Le stock conditionne la possibilité de commander un produit. | Il reste 5 unités disponibles pour un produit donné. |
| ServiceLogistique | Entité organisationnelle responsable de la préparation et de l’expédition des commandes. Elle assure la gestion physique des colis. | L’entrepôt qui prépare les colis clients. |
| PréparationCommande | Processus métier consistant à rassembler les produits d’une commande. Elle précède l’expédition. | Un préparateur rassemble les articles d’une commande. |
| Expédition | Action de confier un colis à un transporteur pour livraison. Elle marque la sortie du colis du système logistique. | Le colis est remis au transporteur. |
| Livraison | Étape finale où la commande est remise au client. Elle clôt le cycle de vie de la commande. | Le colis est livré au domicile du client. |
| Transporteur | Acteur externe chargé de la livraison physique des colis. Il intervient après l’expédition. | Un service de livraison comme un prestataire logistique. |
| AdresseLivraison | Information métier décrivant le lieu de livraison d’une commande. Elle est utilisée pour l’acheminement du colis. | Adresse du domicile du client. |
| StatutCommande | État métier d’une commande dans son cycle de vie. Il permet de suivre son avancement. | Créée, confirmée, préparée, expédiée, livrée. |
| Notification | Message envoyé au client pour l’informer d’un événement métier. Elle accompagne le cycle de vie de la commande. | Email de confirmation de commande. |
| AnnulationCommande | Processus métier consistant à invalider une commande. Il peut être déclenché par un client ou le système. | Commande annulée suite à un paiement refusé. |
| IncidentLivraison | Problème survenant lors de la livraison d’une commande. Il nécessite un traitement spécifique. | Colis perdu ou livraison en retard. |
| Facturation | Processus de génération de documents financiers liés à une commande. Il assure la traçabilité comptable. | Génération d’une facture PDF pour une commande. |
| PrestatairePaiement | Service externe qui gère les transactions financières. Il valide ou refuse les paiements. | Plateforme bancaire externe utilisée pour payer. |



## Termes par contexte

| Terme | Contexte principal | Commentaire (facultatif) |
|-------|-----------------|---------------------------|
| Client | ContexteCommande | Principal acteur du Core Domain. |
| Produit | ContexteStock | Gestion du catalogue et stock. |
| Catalogue | ContexteStock | Partie visible du stock disponible pour commande. |
| Panier | ContextePanier | Permet la sélection des produits avant commande. |
| Commande | ContexteCommande | Coeur du Core Domain. |
| CommandeId | ContexteCommande | Identifiant unique pour tracer les commandes. |
| Paiement | ContextePaiement | Externalisé, Generic Domain. |
| TransactionPaiement | ContextePaiement | Détails de la transaction. |
| Stock | ContexteStock | Vérifie la disponibilité avant création de commande. |
| ServiceLogistique | ContexteLogistique | Prépare et expédie les commandes. |
| PréparationCommande | ContexteLogistique | Processus de collecte des articles. |
| Expédition | ContexteLogistique | Transmission au transporteur. |
| Livraison | ContexteLivraison | Dernière étape du cycle de commande. |
| Transporteur | ContexteLivraison | Externe, gère la livraison physique. |
| AdresseLivraison | ContexteLivraison | Lieu de réception de la commande. |
| StatutCommande | ContexteCommande | Permet de suivre l’état de la commande. |
| Notification | ContexteNotification | Transmet les événements clés aux clients. |
| AnnulationCommande | ContexteCommande | Invalide la commande dans le Core Domain. |
| Facturation | ContexteFacturation | Génération de documents standardisés. |
| PrestatairePaiement | ContextePaiement | Service externe pour paiement. |




