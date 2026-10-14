# Rituel — suivi d'habitudes mobile

> Exemple de CLAUDE.md d'un projet **mobile natif** après /start (Identité) + /architect (Stack). Niveau **STANDARD**, deux codebases (iOS pilote, Android ensuite), sans backend.

## Identité

<!-- start:identité -->
**Rituel** est une app mobile de suivi d'habitudes quotidiennes pour des particuliers qui veulent tenir 3 à 5 habitudes sans friction : une coche par jour, une série (streak) visible, un rappel par habitude. Tout est local à l'appareil, pas de compte.

project_type: mobile
<!-- /start:identité -->

## Stack

<!-- architect:stack -->
- iOS : Swift + SwiftUI, SwiftData (stockage local), iOS 17 minimum, Xcode
- Android : Kotlin + Jetpack Compose (Material 3), Room (stockage local), Android 10 (API 29) minimum, Android Studio
- Notifications : locales (UNUserNotificationCenter / WorkManager + NotificationManager), pas de push serveur
- Backend : aucun en V1 (pas de compte, pas de sync)
- ui_tests: Maestro (flows YAML, un seul jeu pour iOS + Android) pour l'E2E ; XCTest / XCUITest (iOS) et JUnit + Compose UI tests (Android) pour le natif
- Distribution : TestFlight (iOS) puis App Store ; Google Play internal testing puis production
- Machine de build : macOS + Xcode pour iOS ; Android Studio (tout OS) pour Android
<!-- /architect:stack -->

## Request Classification

**Niveau choisi pour ce projet** : `STANDARD` — 4 phases, plateforme pilote d'abord (iOS), portage Android ensuite. `/design` (DESIGN.md web) non applicable : direction visuelle via `design-ui-proposal` (HIG + Material 3).

## Conventions

- Swift : PascalCase pour les types, un type principal par fichier ; Kotlin : conventions du skill `lang-kotlin-android`
- Logique métier (calcul des séries, règles de rappel) isolée de l'UI et testée en unitaire sur les deux plateformes
- Commits conventionnels en anglais (`feat:`, `fix:`, `chore:`)

## Instructions

- Une plateforme à la fois : ne jamais démarrer le portage Android avant la fin de la Phase 2 iOS
- Aucune donnée ne quitte l'appareil en V1 ; aucun SDK d'analytics ou de pub
- Toute permission système (notifications) est demandée au moment où elle est utile, avec un texte d'usage clair
- Pas de dépendance ajoutée sans accord explicite (nom, raison, alternative native)
- Vérification visuelle : simulateur iOS / émulateur Android (skill `tool-mobile-visual-check`), clair et sombre, taille de police par défaut et agrandie

## Contexte métier

- Une « série » = nombre de jours consécutifs où l'habitude est cochée ; elle repart à 0 si un jour est manqué
- Le « jour » change à minuit **heure locale** de l'appareil (attention aux changements de fuseau)
- On peut corriger la veille, pas plus loin (évite de tricher sur les séries)
