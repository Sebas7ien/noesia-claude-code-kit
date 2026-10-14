# STRUCTURE.md — Carte d'architecture du projet

> Exemple de STRUCTURE.md d'un projet desktop (Swift Package, macOS). Rempli par `/architect` Étape 6.5, enrichi par `/evoluer`.

## Arborescence

<!-- architect:directories -->
- `Sources/Jarvis/` — exécutable : app menu bar SwiftUI, point d'entrée, câblage
- `Sources/JarvisCore/` — bibliothèque testable : routeur local/cloud, détection de langue, garde-fou de confirmation
- `Tests/JarvisCoreTests/` — tests XCTest de JarvisCore
- `scripts/` — packaging : `build-app.sh` (`dist/Jarvis.app`), `build-pkg.sh` (`dist/Jarvis-<version>.pkg`)
- `docs/` — PRD, plans (`docs/plans/`), SPECs d'évolution (`docs/specs/`)
<!-- /architect:directories -->

## Patterns clés

<!-- architect:patterns -->
- Pipeline voix : mot d'activation (VAD + STT) → STT → routeur d'intentions → LLM (Ollama ou OpenRouter) → TTS
- Toute action sensible passe par le garde-fou de confirmation vocale
- Secrets uniquement dans le Keychain ; aucun accès réseau sauf Ollama local et OpenRouter
<!-- /architect:patterns -->

## Tests

<!-- architect:tests -->
- XCTest dans `Tests/JarvisCoreTests/`, un fichier `{Type}Tests.swift` par type testé
- Lancer : `DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer swift test`
- Pas de Playwright : projet natif, captures et essais manuels des parcours voix (skill `tool-desktop-visual-check` si UI)
<!-- /architect:tests -->

## Conventions d'arborescence

<!-- architect:conventions -->
- Un type principal par fichier, nommé comme le type (`LanguageDetector.swift`)
- Cible macOS 14 minimum (`.macOS(.v14)`) ; Swift 6
<!-- /architect:conventions -->

## Intégrations externes

<!-- structure:integrations -->
- Ollama (LLM local, HTTP `localhost:11434`) — livré
- WhisperKit (STT local) — livré Phase 1
- OpenRouter (LLM cloud, outil `web_search`) — livré Phase 3 ; clé dans le Keychain
- EventKit (agenda), NSWorkspace (ouvrir des apps) — livré Phase 2
<!-- /structure:integrations -->

## Fichiers clés

<!-- structure:key-files -->
- `Package.swift` — cibles Jarvis, JarvisCore, JarvisCoreTests
- `Sources/JarvisCore/Action.swift` — protocole `Action` + `ActionRegistry`
- `Sources/JarvisCore/SecretStore.swift` — `SecretStore` + `KeychainSecretStore`
- `docs/plans/phase-N-bench.md` — mesures par phase (latence, coût)
<!-- /structure:key-files -->

## Évolutions livrées (résumé)

<!-- structure:evolutions-summary -->
- V2 (2026-10-07) — Mot d'activation « Mac » : remplace « Jarvis », mal transcrit par Whisper (cf docs/specs/SPEC-2026-10-07-wakeword-mac.md)
<!-- /structure:evolutions-summary -->
