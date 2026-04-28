# Bounded Contexts

| Bounded Context | Type | Rôle / Responsabilité principale |
|----------------|------|----------------------------------|
| ContexteCommande | Core | Responsable de la gestion du cycle de vie des commandes. Il gère la création des commandes, leur statut, leur évolution et leur orchestration globale. Ce contexte coordonne les interactions avec les autres contextes (paiement, logistique, livraison). Il porte la logique métier centrale du système. |
| ContexteOrchestration | Core | Responsable de la coordination des processus métiers liés au cycle de vie d’une commande. Il déclenche les commandes vers les autres contextes et gère les transitions d’état métier. Il assure la cohérence globale du flux métier. |
| ContexteStock | Supporting | Gère la disponibilité des produits et les niveaux de stock. Il valide la faisabilité des commandes et informe les autres contextes en cas de rupture. Il supporte directement le Core Domain. |
| ContexteLogistique | Supporting | Responsable de la préparation physique des commandes. Il organise la préparation, l’emballage et la transmission aux transporteurs. Il assure l’exécution opérationnelle des commandes. |
| ContexteLivraison | Supporting | Gère la relation avec les transporteurs et le suivi des livraisons. Il assure la traçabilité des colis jusqu’au client final. |
| ContextePanier | Supporting | Responsable de la gestion du panier client et de l’intention d’achat. Il structure les produits sélectionnés avant la création de commande. |
| ContextePaiement | Generic | Gère les transactions financières via des prestataires externes. Il valide, refuse ou confirme les paiements. Ce contexte est standardisé et fortement externalisé. |
| ContexteNotification | Generic | Responsable de l’envoi des messages aux clients (emails, SMS, notifications). Il diffuse les événements métier importants. |
| ContexteFacturation | Generic | Gère la génération des documents financiers et la traçabilité comptable. Fonction standard dans les systèmes e-commerce. |
