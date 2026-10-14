# Plan — Phase 1 : Squelette iOS + une habitude de bout en bout

> PRD parent : `PRD.md`
> Date : 2026-10-14
> Plateforme : iOS (pilote) · Tests : XCTest + Maestro (flow `creer-habitude`, `cocher-habitude`)

## Contexte (extrait du PRD)

Objectif : on peut créer une habitude, la cocher pour aujourd'hui, la retrouver après relance de l'app, et voir une série correcte. Une seule plateforme (iOS), pas de rappel ni d'historique encore.

Données : locales uniquement (SwiftData). Aucune donnée ne quitte l'appareil.

## Tâches

- [ ] **1. Projet Xcode + écran du jour vide** — Crée le projet SwiftUI `Rituel` (iOS 17 minimum), un `TabView` ou écran unique « Aujourd'hui » avec état vide (« Aucune habitude. Ajoutes-en une. »). **Fait quand** : `xcodebuild -scheme Rituel -destination 'platform=iOS Simulator,name=iPhone 16' build` passe, l'app se lance sur simulateur et affiche l'état vide en clair et en sombre (captures prises avec `tool-mobile-visual-check`).

- [ ] **2. Modèle `Habit` + persistance SwiftData** — Entité `Habit` (id, nom, createdAt, archivedAt?) et `HabitCheck` (habitId, jour calendaire local). **Fait quand** : un test unitaire crée une habitude, la relit depuis un `ModelContainer` en mémoire, et vérifie qu'un check est unique par (habitude, jour).

- [ ] **3. Calcul de série (`StreakCalculator`)** — Module pur : série en cours et meilleure série à partir d'une liste de jours cochés et du jour courant. **Fait quand** : `StreakCalculatorTests` couvre 8 cas (aucune coche, aujourd'hui seul, jours consécutifs, jour manqué, hier coché mais pas aujourd'hui, correction de la veille, changement de fuseau, passage d'heure d'été) et passe (`xcodebuild test`).

- [ ] **4. Créer une habitude** — Bouton « + » → feuille avec champ nom (validé : non vide, ≤ 40 caractères), enregistrer. **Fait quand** : le flow Maestro `creer-habitude.yaml` passe sur simulateur (taper +, saisir « Boire de l'eau », valider, voir la ligne dans la liste), et un nom vide affiche une erreur inline (pas d'alerte bloquante).
  **Sécurité :** le nom est traité comme du texte brut (aucun HTML / URL interprété) et sa longueur est bornée côté modèle, pas seulement côté UI.

- [ ] **5. Cocher / décocher + affichage de la série** — Un tap sur la ligne coche pour aujourd'hui (haptique léger), un second tap décoche ; la série s'affiche sous le nom. **Fait quand** : le flow Maestro `cocher-habitude.yaml` passe, l'état coché survit à la relance de l'app (kill + relance dans le flow), et VoiceOver annonce « Boire de l'eau, coché, série de 1 jour ».

## Critère de phase complète

- [ ] Toutes les tâches 1 à 5 sont cochées
- [ ] `xcodebuild test` : 0 échec ; flows Maestro `creer-habitude` et `cocher-habitude` verts sur simulateur
- [ ] Parcours complet créer → cocher → relancer → série correcte, en mode clair et sombre
- [ ] Aucune permission système demandée dans cette phase (les notifications arrivent en Phase 2)

## Prochaine étape

`/execute docs/plans/phase-1-plan.md`
