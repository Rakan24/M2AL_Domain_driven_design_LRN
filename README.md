# TP Fil Rouge — Domain-Driven Design (DDD)

## E-commerce & Livraison

Projet réalisé dans le cadre de l'UE Domain-Driven Design (M2 AL).

---

## Scénario choisi

**E-commerce & livraison** — Plateforme de vente de produits physiques à destination de clients particuliers, avec gestion complète du cycle de vie des commandes : panier, paiement, préparation logistique, expédition et livraison.

---

## Structure du dépôt

```
.
├── docs/
│   ├── domaine.md                    # TP 1.1 — Scénario et description du domaine
│   ├── event-storming.png            # TP 1.2 — Event Storming express
│   ├── ubiquitous-language.md        # TP 1.3 & 2.3 — Glossaire métier (v1 + v2)
│   ├── domain-overview.md            # TP 1.4 — Identification des sous-domaines
│   ├── bounded-contexts.md           # TP 2.1 — Bounded Contexts
│   ├── context-map.md                # TP 2.2 — Context Map & patterns de relation
│   ├── domain-concepts.md            # TP 2.4 — Alignement conceptuel du modèle
│   ├── domain-model.md               # TP 3.1 — Entités & Objets Valeur
│   ├── invariants.md                 # TP 3.2 — Agrégats & Invariants
│   ├── tests-domaine.md              # TP 3.3 — Tests de domaine Given/When/Then
│   ├── architecture-hexagonale.md    # TP 4.1 — Architecture hexagonale
│   ├── repositories.md               # TP 4.2 — Repositories conceptuels
│   ├── api-examples.md               # TP 4.3 — Exposition REST (maquettes)
│   ├── integration-scenarios.md      # TP 4.4 — Tests d'intégration
│   ├── contracts.md                  # TP 5.1 — Contrats d'échange entre contexts
│   ├── integration-design.md         # TP 5.2 — Design des intégrations
│   ├── observability.md              # TP 5.3 — Observabilité conceptuelle
│   ├── scenario-final.md             # TP 5.4 — Scénario complet inter-contextes
│   ├── deployment-vision.md          # TP 6.1 — Vision de déploiement Docker
│   ├── architecture-runtime.md       # TP 6.2 — Orchestration Docker Compose
│   ├── dashboard-grafana.png         # TP 6.3 — Dashboard Grafana (mockup)
│   └── presentation-support.pptx    # TP 6.4 — Support de présentation orale
└── README.md
```

---

## Bounded Contexts identifiés

| Bounded Context | Type |
|---|---|
| ContexteCommande | Core |
| ContexteOrchestration | Core |
| ContexteStock | Supporting |
| ContexteLogistique | Supporting |
| ContexteLivraison | Supporting |
| ContextePanier | Supporting |
| ContextePaiement | Generic |
| ContexteNotification | Generic |
| ContexteFacturation | Generic |

---

## Auteurs

Projet réalisé en binôme — M2 AL
