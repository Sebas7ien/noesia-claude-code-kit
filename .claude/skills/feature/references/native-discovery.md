# Découverte d'un projet natif (`mobile` / `desktop`) — Étape 1 de /feature

> **Lis ce fichier uniquement si** l'Étape 1 de `SKILL.md` détecte un projet natif (`Package.swift`, `*.xcodeproj`, `build.gradle*`, `AndroidManifest.xml`, `*.csproj`, `Cargo.toml`, `src-tauri/`, `pubspec.yaml`, Electron). Lecture seule, comme le reste de l'Étape 1.

## Quoi lire, par plateforme

| Plateforme | Manifeste de build | Permissions / droits | Signature et distribution |
|------------|--------------------|----------------------|---------------------------|
| iOS / macOS | `Package.swift`, `*.xcodeproj` (schemes, deployment target) | `Info.plist` (`NS*UsageDescription`), `*.entitlements` | Team ID, profils, hardened runtime, notarisation (macOS) |
| Android | `build.gradle.kts` (`minSdk`, `targetSdk`), `settings.gradle.kts` | `AndroidManifest.xml` (`uses-permission`, composants `exported`) | `signingConfigs` (le keystore doit être hors du repo), `.aab` / `.apk` |
| Windows | `*.csproj`, `*.sln` | `Package.appxmanifest` (capabilities) | certificat de signature, MSIX / installeur |
| Linux | `Cargo.toml`, `meson.build` | manifeste Flatpak (`finish-args`), fichier `.desktop` | Flatpak, AppImage, paquet distro |
| Tauri | `src-tauri/Cargo.toml`, `src-tauri/tauri.conf.json` | `src-tauri/capabilities/*.json` (portée fs, commandes exposées au front) | bundles par OS, updater |
| Electron | `package.json` (`main`), config du bundler | `contextIsolation`, `nodeIntegration`, preload, IPC | électron-builder / forge, signature par OS |
| Cross-platform mobile | `package.json` (React Native / Expo), `pubspec.yaml` (Flutter) | idem iOS + Android, dans les dossiers natifs | idem |

## À extraire pour le brief

- **`project_type`** : `mobile` ou `desktop` (jamais `webapp`, même si l'UI est une WebView).
- **Plateformes ciblées** et celles **buildables sur cette machine** (ex : WinUI ne se build pas sur macOS). Une cible non buildable est notée comme risque, pas ignorée.
- **`ui_tests:`** : outil de test UI retenu, par OS pour le desktop (`CLAUDE.md ## Stack`, sinon cherche `androidTest/`, `.maestro/`, un target *UI Testing Bundle*, `e2e/wdio.conf.ts`). Aucun outil → note-le dans « Contraintes » et renvoie vers `/architect` (ou `/start` 5a) ; ne propose **jamais** Playwright pour une app native.
- **Livré ou non** : présence d'une distribution (TestFlight, Play, DMG, MSIX, Flatpak) ou d'un workflow de release.

## Surfaces sensibles propres au natif

À ajouter à la liste de l'Étape 2 quand elles sont touchées :

- **Permission OS** nouvelle ou élargie (notifications, micro, fichiers, localisation, alarmes exactes) : libellé d'usage honnête, demande au bon moment, dégradation si refus.
- **Système de fichiers** : chemin choisi par l'utilisateur, portée autorisée, écrasement, traversée de chemin.
- **Secrets et données locales** : Keychain / Keystore / Credential Manager plutôt que préférences en clair ; base locale non chiffrée.
- **Points d'entrée externes** : deep links, URL schemes, intents / composants `exported`, protocoles personnalisés.
- **Pont front ↔ natif** (Tauri, Electron, WebView) : commandes ou API exposées au code web, contenu distant chargé, CSP.
- **Mise à jour et signature** : updater, clés de release ou keystore dans le repo.

Si `docs/security-checklist.md` est absente, le brief note « baseline seule » comme d'habitude. Quelques règles typiques à suggérer à l'utilisateur pour un projet natif sont dans les exemples `examples/feature-mobile-existant/docs/security-checklist.md` et `examples/feature-desktop-existant/docs/security-checklist.md`.
