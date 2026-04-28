# Vue d'ensemble du domaine

## Liste des fonctionnalités

- Gestion du catalogue produits  
- Gestion du panier  
- Gestion des commandes  
- Gestion des paiements  
- Gestion des stocks  
- Préparation logistique des commandes  
- Gestion des livraisons  
- Notifications clients  
- Facturation  
- Gestion des incidents et retours  




## Classification des sous-domaines

| Fonctionnalité | Type | Justification |
|---------------|------|---------------|
| Gestion des commandes | Core Domain | La commande est le cœur du système e-commerce. Elle structure l’ensemble des processus métiers : paiement, logistique, livraison et facturation. Elle porte la valeur principale de l’activité. |
| Orchestration du cycle de vie de la commande | Core Domain | La coordination entre les étapes métier (création, paiement, préparation, expédition, livraison) constitue un savoir-faire différenciant. C’est ce qui garantit la fiabilité et la qualité du service. |
| Gestion des stocks | Supporting Domain | Indispensable au bon fonctionnement du système, la gestion des stocks soutient directement la commande mais ne constitue pas un avantage concurrentiel en soi. |
| Préparation logistique | Supporting Domain | Nécessaire pour exécuter les commandes, mais standardisée dans de nombreux systèmes e-commerce. Elle supporte le Core sans être différenciante. |
| Gestion des livraisons | Supporting Domain | Essentielle pour la satisfaction client, mais largement standardisée via des prestataires externes. |
| Gestion du panier | Supporting Domain | Fonction nécessaire pour permettre la commande, mais très générique dans les systèmes e-commerce. |
| Paiement | Generic Domain | Le paiement est une fonctionnalité standardisée et externalisée via des prestataires spécialisés. Elle n’est pas différenciante. |
| Notifications | Generic Domain | Fonction transversale standard, utilisée dans la majorité des systèmes. |
| Facturation | Generic Domain | Processus standardisé, souvent outillé par des solutions existantes. |
| Gestion des retours | Generic Domain | Fonction métier commune à la plupart des plateformes e-commerce. |







