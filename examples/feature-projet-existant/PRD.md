# PRD — factures-api (reconstitué par /feature)

> ⚠️ Reconstitué depuis le code existant le 2026-10-14. À relire : la Vision et les Personas sont des hypothèses confirmées en conversation, pas une spec d'origine.

**Niveau** : STANDARD

## 1. Vision

API de gestion de factures pour un petit cabinet : clients, factures, suivi des paiements, consommée par une interface interne.

## 2. Personas

- **Gestionnaire du cabinet** — crée les factures et suit les paiements. Douleur : relancer les impayés à la main.

## 3. Scope actuel (V2)

### Core
- [x] CRUD clients
- [x] Création et consultation de factures
- [x] Marquer une facture payée
- [ ] Relance e-mail des factures impayées (cf docs/features/2026-10-14-relance-factures-impayees.md)

### Technique
- [x] Validation Zod aux frontières
- [x] Requêtes SQL paramétrées
- [ ] Job planifié quotidien

## 4. Hors scope (différé)

- [ ] Authentification de l'interface — hors périmètre de cette phase
- [ ] Génération PDF des factures

## 5. Constraints non-négociables

- Aucune donnée client dans les logs ; aucun secret dans le repo
- Un e-mail de relance ne part jamais deux fois pour la même facture

## 6. Success Criteria

- Aucune facture échue de plus de 7 jours ne reste sans relance
- Zéro double envoi sur un mois de fonctionnement

## 7. Implementation Phases

**V1 (existant, reconstitué)** — CRUD clients/factures, suivi des paiements (état avant /feature)
**V2 (en cours)** — Relance e-mail des factures impayées (cf docs/features/2026-10-14-relance-factures-impayees.md)

## 8. Risks & Mitigations

- **Risque** : double envoi → **Mitigation** : `reminded_at` mis à jour dans la même transaction.
- **Risque** : relance d'une facture déjà payée → **Mitigation** : re-vérifier le statut avant l'envoi.
- **Risque** : dépendance e-mail non choisie → **Mitigation** : décision validée avant `/plan` (ADR si Resend retenu).
