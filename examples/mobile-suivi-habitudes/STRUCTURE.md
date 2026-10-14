# STRUCTURE.md — Carte d'architecture du projet

> Exemple de STRUCTURE.md d'un projet mobile à deux codebases natives. Rempli par `/architect` Étape 6.5.

## Arborescence

<!-- architect:directories -->
- `ios/Rituel/` — app SwiftUI (vues, modèles SwiftData, services)
- `ios/RituelTests/` — tests unitaires XCTest (séries, rappels)
- `ios/RituelUITests/` — tests XCUITest ciblés (écran du jour)
- `android/app/` — app Kotlin / Compose (écrans, entités Room, ViewModels) — créé en Phase 3
- `e2e/maestro/` — flows Maestro partagés iOS + Android (`creer-habitude.yaml`, `cocher-habitude.yaml`)
- `docs/` — PRD, plans (`docs/plans/`), briefs et SPECs
<!-- /architect:directories -->

## Patterns clés

<!-- architect:patterns -->
- Logique des séries dans un module pur (sans UI, sans framework), un par plateforme, mêmes cas de test
- Écran = vue + ViewModel / état observable ; la vue ne calcule rien
- Rappels : un identifiant de notification stable par habitude (annuler / reprogrammer sans doublon)
<!-- /architect:patterns -->

## Tests

<!-- architect:tests -->
- Unitaires : XCTest (iOS), JUnit (Android) — priorité aux séries et au changement de jour
- UI : Maestro pour les parcours, XCUITest / Compose UI tests pour les écrans critiques
- Vérification visuelle : simulateur / émulateur via `tool-mobile-visual-check`
<!-- /architect:tests -->

## Intégrations externes

<!-- structure:integrations -->
- Aucune en V1 (pas de backend, pas d'analytics)
<!-- /structure:integrations -->

## Fichiers clés

<!-- structure:key-files -->
- `ios/Rituel/Models/Habit.swift` — entité SwiftData d'une habitude
- `ios/Rituel/Domain/StreakCalculator.swift` — calcul des séries (pur, testé)
- `e2e/maestro/` — flows E2E partagés
<!-- /structure:key-files -->

## Évolutions livrées (résumé)

<!-- structure:evolutions-summary -->
_(Maintenu par /evoluer après chaque livraison.)_
<!-- /structure:evolutions-summary -->
