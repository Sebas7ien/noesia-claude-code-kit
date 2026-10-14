# liste-courses

> Exemple de CLAUDE.md complété par `/feature` (Identité + Stack déduites du code, validées par l'utilisateur).

## Identité

<!-- start:identité -->
Application Android de liste de courses partagée dans un foyer : ajout rapide d'articles, cases à cocher, mode hors-ligne. Usage perso, installée en APK de test, pas encore publiée sur le Play Store.

project_type: mobile
<!-- /start:identité -->

## Stack

<!-- architect:stack -->
- Kotlin 2 + Jetpack Compose (Material 3), `minSdk 26`, `targetSdk 35`
- Room (SQLite local), pas de backend
- Tests : JUnit (logique), Compose UI tests (`androidTest/`) ; E2E : Maestro (`.maestro/`)
- ui_tests: android: Compose UI tests + Maestro
- Distribution : non définie (APK de test uniquement)
<!-- /architect:stack -->

## Request Classification

**Niveau choisi pour ce projet** : `STANDARD`

## Conventions

- Un écran = un `@Composable` + un `ViewModel` ; état exposé en `StateFlow`
- Accès base uniquement via les DAO Room, jamais depuis un composable
- Commits conventionnels en anglais
