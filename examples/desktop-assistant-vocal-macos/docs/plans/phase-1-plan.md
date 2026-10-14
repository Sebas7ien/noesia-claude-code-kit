# Plan — Phase 1 : Boucle voix de bout en bout

> PRD parent : `PRD.md`
> Plateforme : macOS (pilote unique) · Tests : XCTest sur `JarvisCore` + vérification manuelle des parcours voix
> Date : 2026-10-06

## Tâches

- [x] **1. App menu bar + machine à états** — Fait quand : `Sources/JarvisCore/AssistantState.swift` définit
  idle/listening/thinking/speaking avec transitions valides, `AssistantStateTests` passe
  (`DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer swift test`), et `swift run Jarvis` affiche une icône
  menu bar dont le symbole change selon l'état (item de menu « Simuler »).
- [x] **2. Capture micro + permission Micro** — Fait quand : l'app demande l'accès Micro au 1er lancement
  (`NSMicrophoneUsageDescription` via Info.plist embarqué avec `-sectcreate`), un raccourci global / item de menu
  démarre et arrête l'enregistrement, et un log affiche la durée du buffer 16 kHz mono capturé.
  (Risque : si l'Info.plist embarqué ne suffit pas, bascule vers un projet Xcode — à signaler.)
  **Sécurité :** la chaîne d'usage Micro est explicite et honnête (`NSMicrophoneUsageDescription`), aucun buffer audio n'est écrit sur disque ni loggé au-delà de sa durée.
- [x] **3. STT WhisperKit + détection FR/EN** — Fait quand : dépendance `argmaxinc/WhisperKit` ajoutée à
  `Package.swift` (après accord explicite), modèle large-v3-turbo téléchargé au 1er lancement, une phrase dite via le
  raccourci s'affiche en texte avec sa langue détectée (`fr`/`en`) dans le log ; `LanguageResolver`
  (repli sur la langue précédente si confiance faible) testé par `LanguageResolverTests`.
- [x] **4. Client Ollama** — Fait quand : `OllamaClient` (JarvisCore) appelle `POST localhost:11434/api/chat`
  en streaming avec un prompt système « réponds dans la langue de l'utilisateur, court et oral »,
  `OllamaClientTests` (URLProtocol mocké) passe, un appel réel renvoie une réponse (log), et une erreur claire
  est levée si Ollama est éteint.
  **Sécurité :** l'URL est restreinte à `localhost` (pas d'hôte configurable depuis une entrée utilisateur) ; le texte transcrit n'est jamais passé à un shell.
- [x] **5. Benchmark modèle Ollama** — Fait quand : latence au 1er token + totale mesurée pour `gpt-oss:20b` sur
  5 prompts FR/EN, résultats notés dans `docs/plans/phase-1-bench.md` ; si > 3 s au 1er token, un modèle 3-8B est
  proposé (pull seulement après accord) et le modèle retenu est configurable (`UserDefaults`, clé `ollamaModel`).
- [x] **6. TTS système bilingue** — Fait quand : `Speaker` lit un texte avec une voix FR ou EN selon la langue
  détectée, `VoiceSelector` (choix de voix par langue) testé par `VoiceSelectorTests`, et la voix s'entend à
  l'oreille sur une phrase FR puis une phrase EN.
- [x] **7. Boucle complète via raccourci** — Fait quand : raccourci → parole → transcription → réponse Ollama →
  réponse parlée dans la bonne langue, icône suivant les 4 états, et sur 5 phrases de test locales simples le début
  de la réponse vocale arrive en < 3 s (mesuré par log horodaté).
- [x] **8. Wake word « Jarvis » (VAD + STT)** — Fait quand : `WakeWordMatcher` (tolère « Jarvis », « Jarvice »,
  etc.) testé par `WakeWordMatcherTests`, une VAD à seuil d'énergie ne passe au STT que sur de la parole, dire
  « Jarvis, quelle heure est-il ? » déclenche la boucle sans raccourci, et 10 min de silence de bureau donnent
  0 faux déclenchement.
  _Vérifié : test synthétique `say` → VAD → Whisper → matcher ; « Jervis, quelle heure est-il ? » dit au micro
  déclenche la boucle ; 10 min de frappes clavier = 0 passage STT, 0 déclenchement (après correction de la VAD)._

## Critère de phase complète

- [x] Toutes les tâches 1 à 8 sont cochées
- [x] `swift test` : 0 échec
- [x] Conversation vocale FR puis EN, bout en bout, sans toucher au clavier (wake word) ; résultats du benchmark
  STT/latence/modèle consignés pour valider les hypothèses du PRD §8
- [x] Détection FR/EN essayée sur quelques phrases mixtes ; l'échantillon complet de 40 phrases reste en Phase 4

## Validation Phase 1

### Méthode utilisée
- C (autre) : app native macOS — tests automatiques + essais vocaux réels.

### Tests réalisés
- [x] `swift build` OK ; `swift test` : 43 tests, 0 échec (tests live STT + Ollama inclus)
- [x] Wake word synthétique (`say` → VAD → Whisper → matcher) : 2 déclenchements attendus, 2 phrases ignorées
- [x] Conversation FR par wake word au micro (« Jervis, quelle heure est-il ? » → réponse en français)
- [x] Conversation EN par wake word au micro (« Hey Jarvis, what time is it? » → réponse en anglais, parole à +2,95 s)
- [x] Phrases mixtes essayées (« Open Safari, please » → en, « Ouvre Safari please » → fr)
- [x] Benchmark Ollama consigné (`phase-1-bench.md`) : 1er token 0,7-2,0 s avec `think: "low"`
- [x] 10 min de frappes clavier : 0 passage STT, 0 déclenchement (après correction de la VAD)
- [x] 5 phrases simples : début de parole 1,63-2,79 s (< 3 s)

### Verdict
- **✅ OK** — réserves (FR et EN validés en sessions séparées, latence à froid ~11 s, VAD validée sur un seul environnement) notées dans « Découvertes ».

## Prochaine étape

`/plan Phase 2`
