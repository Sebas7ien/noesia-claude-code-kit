# Jarvis — assistant vocal macOS

> Exemple de CLAUDE.md d'un projet **desktop natif** après /start (Identité) + /architect (Stack). Tiré d'un vrai projet (app menu bar Swift), anonymisé. Niveau **STANDARD**, une seule plateforme (macOS).

## Identité

<!-- start:identité -->
**Jarvis** est un assistant vocal de bureau pour macOS qui exécute ce qu'on lui dit, pour un usage personnel (un seul utilisateur). Application native Mac, conversation vocale bilingue français/anglais avec détection automatique de la langue, écoute active par mot d'activation, réponses à voix haute.

project_type: desktop
<!-- /start:identité -->

## Stack

<!-- architect:stack -->
- App native macOS : Swift 6 + SwiftUI, app « menu bar » (icône d'état), cible macOS 14 minimum
- STT local : WhisperKit (détection auto FR/EN) ; mot d'activation via VAD + STT
- TTS : voix système (AVSpeechSynthesizer)
- LLM : Ollama local par défaut, OpenRouter en renfort (recherche)
- Actions Mac : NSWorkspace, EventKit (agenda) ; confirmation vocale obligatoire
- Secrets : Keychain macOS
- ui_tests: macos: XCTest sur `JarvisCore` (logique sans UI) + vérification manuelle des parcours voix
  (XCUITest écarté : app menu bar pilotée à la voix, peu d'UI à automatiser)
- Distribution : `.pkg` installant l'app dans /Applications (non notarisé, usage perso)
- Machine de build : macOS + Xcode.app (les Command Line Tools seuls n'ont pas XCTest)
<!-- /architect:stack -->

## Request Classification

**Niveau choisi pour ce projet** : `STANDARD` — 5 phases, PRD complet, `/challenge` optionnel. Pas de `/design` (DESIGN.md web non applicable) ; direction visuelle = conventions macOS (HIG).

## Conventions

- Types et fichiers Swift en PascalCase, un type principal par fichier
- Logique métier dans `JarvisCore` (testable, sans UI) ; `Jarvis` ne fait que câblage et UI
- Code, commentaires et commits en anglais (`feat:`, `fix:`, `chore:`)

## Instructions

- Lancer les tests : `DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer swift test`
- Toute action sensible passe par le garde-fou de confirmation vocale, jamais d'exécution directe
- Secrets uniquement dans le Keychain ; aucune clé dans le repo ni dans `UserDefaults`
- Pas de dépendance ajoutée sans accord explicite (nom, raison, alternative)

## Contexte métier

- La voix (STT + TTS) reste locale ; seul le texte de la requête part vers le cloud
- Jamais d'envoi automatique d'e-mail ou de message
- Anglais et français : conversation fluide dans les deux
