# carnet-notes

> Exemple de CLAUDE.md complété par `/feature` (Identité + Stack déduites du code, validées par l'utilisateur).

## Identité

<!-- start:identité -->
Application de bureau de prise de notes en Markdown : notes locales, recherche instantanée, thème clair/sombre. Usage perso sous Windows et Linux, pas encore distribuée.

project_type: desktop
<!-- /start:identité -->

## Stack

<!-- architect:stack -->
- Tauri 2 : cœur Rust (`src-tauri/`), interface TypeScript + Svelte (`src/`)
- Stockage : fichiers `.md` dans un dossier de données de l'application, index SQLite pour la recherche
- Plateformes : Windows et Linux (macOS non ciblé pour l'instant)
- Tests : `cargo test` (logique Rust), Vitest (interface)
- ui_tests: windows: WebdriverIO + tauri-driver, linux: WebdriverIO + tauri-driver
- Distribution : non définie (build local)
<!-- /architect:stack -->

## Request Classification

**Niveau choisi pour ce projet** : `STANDARD`

## Conventions

- Logique métier en Rust (`src-tauri/src/`), l'interface n'appelle que des commandes Tauri nommées
- Erreurs Rust : type `AppError` sérialisé vers l'interface ; jamais de `unwrap()` dans une commande
- Commits conventionnels en anglais
