# Scénario choisi

E-commerce & livraison

# Contexte métier

Le domaine concerne une plateforme de e-commerce spécialisée dans la vente de produits physiques à destination de clients particuliers. L’organisation est structurée autour de plusieurs pôles : commerce (gestion du catalogue, offres, pricing), logistique (préparation des commandes, stock, expédition), service client et direction commerciale. L’activité repose sur une forte volumétrie de commandes, une exigence élevée en matière de qualité de service et une expérience utilisateur fluide.

Les enjeux principaux sont la fiabilité des processus de commande, la rapidité de livraison, la gestion des stocks en temps réel et la coordination entre les différents acteurs internes et partenaires externes (transporteurs, prestataires de paiement, fournisseurs). Le système doit garantir une cohérence métier forte entre la prise de commande, le paiement, la préparation logistique et la livraison.

Le contexte impose également des contraintes importantes : gestion des pics de charge (soldes, promotions), traçabilité des commandes, sécurité des paiements, respect des délais de livraison et capacité à gérer les incidents (annulations, retards, ruptures de stock, litiges clients).





# Rôles utilisateurs

| Rôle                     | Type         | Description |
|--------------------------|--------------|-------------|
| Responsable e-commerce   | Direction    | Pilote la stratégie commerciale de la plateforme. Il supervise le catalogue, les promotions et les indicateurs de performance. Il s'assure que les objectifs de vente sont atteints et que l'expérience client est optimale. |
| Préparateur de commandes | Opérationnel | Responsable de la préparation physique des commandes en entrepôt. Il prélève les articles en stock, les emballe et les remet au transporteur. Il intervient après la validation du paiement et avant l'expédition. |
| Client                   | Client       | Utilisateur final de la plateforme e-commerce. Il consulte le catalogue, constitue son panier, passe commande et suit la livraison. Il est le principal acteur du flux métier de bout en bout. |



# Problématiques métier

Le système doit garantir la cohérence entre les commandes clients, les paiements et les stocks disponibles afin d’éviter les ruptures ou les ventes impossibles à honorer.  
Il doit permettre une orchestration fluide entre les différents acteurs (client, logistique, paiement, transport) tout en assurant la traçabilité complète du cycle de vie d’une commande.  
La gestion des incidents (échecs de paiement, annulations, retards de livraison, retours produits) doit être intégrée nativement au processus métier.

Problèmes majeurs à adresser :
- Synchronisation entre commande, paiement et stock
- Traçabilité du cycle de vie des commandes
- Gestion des erreurs et exceptions métier
- Coordination entre systèmes internes et services externes
- Fiabilité des livraisons



# Scénario fil rouge

Un client consulte la plateforme e-commerce et parcourt le catalogue de produits disponibles. Il sélectionne plusieurs articles et les ajoute à son panier. Après validation du panier, il renseigne ses informations de livraison et choisit un mode de livraison proposé par le système.  
Le client procède ensuite au paiement de sa commande via un prestataire de paiement externe. Une fois le paiement validé, la commande est confirmée dans le système et un numéro de commande unique est généré.

La commande est alors transmise au service logistique. Les préparateurs de commandes reçoivent la demande, prélèvent les articles en stock et préparent le colis. Une fois la préparation terminée, le colis est confié à un transporteur qui prend en charge la livraison.  
Le client est informé des différentes étapes : confirmation de commande, préparation, expédition, livraison. Une fois le colis livré, la commande est marquée comme terminée dans le système.

Ce scénario représente le flux principal du système, de la demande initiale du client jusqu’à la livraison finale du produit.










