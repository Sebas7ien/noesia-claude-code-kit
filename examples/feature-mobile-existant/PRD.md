# PRD — liste-courses (reconstitué par /feature)

> ⚠️ Reconstitué depuis le code existant le 2026-10-14. À relire : la Vision et les Personas sont des hypothèses confirmées en conversation, pas une spec d'origine.

**Niveau** : STANDARD

## 1. Vision

Application Android de liste de courses pour un foyer, utilisable hors-ligne, avec ajout d'un article en deux gestes.

## 2. Personas

- **Membre du foyer** — fait les courses en sortant du travail. Douleur : oublier d'ouvrir la liste avant de passer au magasin.

## 3. Scope actuel (V2)

### Core
- [x] Ajouter, cocher, supprimer un article
- [x] Plusieurs listes (semaine, fête…)
- [x] Fonctionnement hors-ligne
- [ ] Rappel quotidien par notification locale (cf docs/features/2026-10-14-rappel-quotidien-courses.md)

### Technique
- [x] Persistance Room, accès via DAO
- [x] Tests Compose UI sur l'écran principal
- [ ] Planification des rappels (WorkManager)

## 4. Hors scope (différé)

- [ ] Partage de liste entre plusieurs téléphones (backend requis)
- [ ] Version iOS

## 5. Constraints non-négociables

- Aucune donnée ne quitte le téléphone
- Aucune permission demandée au premier lancement : seulement au moment où l'utilisateur active la fonctionnalité qui en a besoin

## 6. Success Criteria

- Le rappel s'affiche à l'heure choisie, y compris après un redémarrage du téléphone
- Refuser la permission de notification ne bloque aucune autre fonction

## 7. Implementation Phases

**V1 (existant, reconstitué)** — listes, articles, persistance Room, écran principal (état avant /feature)
**V2 (en cours)** — Rappel quotidien par notification (cf docs/features/2026-10-14-rappel-quotidien-courses.md)

## 8. Risks & Mitigations

- **Risque** : rappel perdu après redémarrage ou mode économie d'énergie → **Mitigation** : WorkManager (persistant) et test manuel sur émulateur redémarré.
- **Risque** : refus de la permission (Android 13+) → **Mitigation** : écran de réglage explicite, aucune notification tentée sans permission.
- **Risque** : dépendance `androidx.work` non encore présente → **Mitigation** : signalée avant installation (décision validée avant `/plan`).
