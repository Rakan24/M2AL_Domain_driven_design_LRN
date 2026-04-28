# Context Map

## Schéma général

    - Voir fichier 'TP2.2_Context Map_Schéma général.png'




## Relations et patterns

| Contexte source | Contexte cible | Pattern de relation | Justification |
|-----------------|----------------|---------------------|---------------|
| ContexteCommande | ContextePaiement | Customer/Supplier | ContexteCommande est le client qui exige un service de paiement. ContextePaiement est le fournisseur et doit s'adapter aux besoins de la commande. Cette relation garantit que le flux de validation du paiement est piloté par le domaine métier central. |
| ContexteCommande | ContexteOrchestration | Partnership | Les deux contextes sont Core Domain et collaborent étroitement pour orchestrer le cycle de vie complet d'une commande. Ils partagent un intérêt mutuel et évoluent ensemble. Cette relation implique un alignement fort entre les deux équipes. |
| ContexteOrchestration | ContexteStock | Conformist | ContexteOrchestration consomme les données de stock telles qu'elles sont exposées, sans les transformer. Il se conforme au modèle de ContexteStock pour vérifier la disponibilité des produits avant confirmation. Ce choix évite une complexité inutile d'adaptation. |
| ContexteOrchestration | ContexteLogistique | Conformist | ContexteOrchestration publie des événements que ContexteLogistique consomme directement. Le logistique se conforme au format des événements définis par l'orchestration. Cette approche simplifie l'intégration et réduit le couplage. |
| ContexteLogistique | ContexteLivraison | Customer/Supplier | ContexteLogistique pilote le besoin de livraison et impose ses attentes à ContexteLivraison. Ce dernier est le fournisseur qui s'adapte aux formats de données fournis. La relation est asymétrique car la logistique décide du moment et du mode d'expédition. |
| ContexteCommande | ContexteNotification | Anticorruption Layer | Pour protéger le domaine central des spécificités techniques du système de notification, une couche ACL traduit les événements métier en messages compréhensibles par ContexteNotification. Cela isole le Core Domain des détails d'implémentation des notifications. |
| ContextePaiement | ContexteFacturation | Customer/Supplier | ContextePaiement déclenche la facturation après validation d'une transaction. ContexteFacturation est le fournisseur qui génère les documents selon les données transmises. Cette relation standardisée s'appuie sur une API REST clairement définie. |

---

## Intégrations techniques envisagées

1. **Paiement via REST API**  
   - **Type** : REST  
   - **BC impliqués** : ContexteCommande → ContextePaiement  
   - **Cas d’usage** : Lorsqu’un client valide une commande, le contexte commande envoie une requête REST à ContextePaiement pour effectuer la transaction. La réponse confirme ou refuse le paiement, permettant la suite du flux.

2. **Transmission des commandes à la logistique via événement**  
   - **Type** : Event (publish/subscribe)  
   - **BC impliqués** : ContexteOrchestration → ContexteLogistique  
   - **Cas d’usage** : Lorsqu’une commande est validée et payée, ContexteOrchestration publie un événement 'CommandeConfirmée'. ContexteLogistique s’abonne pour préparer la commande et déclencher l’expédition.

3. **Notifications clients via broker d’événements**  
   - **Type** : Event  
   - **BC impliqués** : ContexteCommande / ContexteLogistique → ContexteNotification  
   - **Cas d’usage** : Chaque événement clé du cycle de vie d’une commande (paiement validé, commande préparée, expédiée, livrée) déclenche un message vers ContexteNotification pour notifier le client par email ou SMS.

4. **Facturation automatisée via REST API**  
   - **Type** : REST  
   - **BC impliqués** : ContextePaiement → ContexteFacturation  
   - **Cas d’usage** : Dès qu’un paiement est validé, ContextePaiement envoie une requête REST à ContexteFacturation pour générer la facture correspondante.

