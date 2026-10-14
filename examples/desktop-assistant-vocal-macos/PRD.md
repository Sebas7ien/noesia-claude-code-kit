# PRD — Jarvis (assistant vocal macOS)

**Niveau** : STANDARD

## 1. Vision

Assistant vocal de bureau pour macOS, à écoute active par wake word, qui converse en
français et en anglais (détection automatique), exécute des actions sur le Mac et répond
à voix haute. LLM local (Ollama) par défaut, cloud (OpenRouter) en renfort. Usage perso.

## 2. Personas

- **Utilisateur unique** — travaille sur Mac toute la journée. Veut déléguer à la
  voix des actions courantes, s'entraîner à l'anglais à l'oral, et faire des recherches
  sans quitter son travail. Douleur : changer de contexte pour ouvrir/chercher/demander.

## 3. Scope actuel (V1)

### Core
- [x] Écoute active : wake word « Jarvis » (VAD + STT local), réponse vocale
- [x] Conversation fluide FR/EN avec détection automatique de la langue
- [x] Ouvrir des applications à la voix
- [x] Résumé des tâches/événements du jour (agenda)
- [x] Mode apprentissage de l'anglais (conversation, corrections orales)
- [x] Recherche via LLM cloud (OpenRouter, Perplexity Sonar)
- [ ] Mot d'activation « Mac » (transcription fiable, ≥ 9/10)

### Technique
- [x] App Swift/SwiftUI « menu bar » (icône d'état : écoute / réflexion / parle)
- [x] STT local WhisperKit ; TTS voix système (AVSpeechSynthesizer)
- [x] LLM local Ollama (HTTP localhost) + routeur local/cloud selon l'intention
- [x] Clé OpenRouter dans le Keychain macOS
- [x] Permissions macOS gérées : Micro, Calendriers, Rappels (Automation non requise par NSWorkspace ; à ajouter si une action AppleScript arrive)
- [ ] Package `.pkg` installant l'app dans /Applications

## 4. Hors scope (différé)

- [ ] Rédaction d'e-mails — V2 ; brouillon uniquement, jamais d'envoi auto
- [ ] TTS local premium (ex. Kokoro) — V2, après avoir jugé les voix système
- [ ] Moteur wake word dédié (Porcupine / openWakeWord) — si batterie/faux positifs gênent
- [ ] Écoute continue sans mot d'activation — trop de faux déclenchements
- [ ] Notarisation Apple / distribution à d'autres — usage perso uniquement

## 5. Constraints non-négociables

- macOS uniquement ; usage personnel, mono-utilisateur
- Toute action destructive ou sortante exige une confirmation vocale explicite
- Aucun envoi automatique d'e-mail ou de message
- Aucune clé en clair dans le repo ni dans les préférences (Keychain)
- Voix (STT + TTS) traitée en local ; seul le texte de la requête part vers le cloud
- DeepSeek réservé aux demandes non sensibles (serveurs hors UE), jamais les e-mails
- Anglais et français : conversation fluide dans les deux

## 6. Success Criteria

- Wake word → début de la réponse vocale en < 3 s sur requête locale simple
- Détection FR/EN correcte sur ≥ 95 % de 40 phrases de test (20 FR, 20 EN)
- Aucune action sensible exécutée sans confirmation (vérifié sur les cas du périmètre)
- Faux déclenchements du wake word : < 1 par heure en bureau calme
- Installation : le `.pkg` produit une app lançable depuis Applications, sans terminal
- Coût OpenRouter mensuel mesuré et jugé acceptable

## 7. Implementation Phases

- **Phase 1 — Boucle voix de bout en bout** ✅ Terminée le 2026-10-07 : app menu bar, wake word, WhisperKit, Ollama,
  TTS système, FR/EN auto. Valide les hypothèses STT/latence/modèle Ollama sur ce Mac.
- **Phase 2 — Actions Mac + confirmation** ✅ Terminée le 2026-10-07 : ouvrir des apps, agenda du jour, permissions,
  garde-fou de confirmation vocale.
- **Phase 3 — Cloud hybride** ✅ Terminée le 2026-10-07 : OpenRouter, outil `web_search` (le LLM local route),
  recherche Sonar, clé dans le Keychain.
- **Phase 4 — Apprentissage de l'anglais + polish** ✅ Terminée le 2026-10-07 : mode apprentissage (commande vocale,
  1 correction max), historique en RAM, réglages (modèle, voix, niveau), erreurs parlées FR/EN, banc FR/EN 39/40.
  Réserve : « Jarvis » est souvent mal transcrit par Whisper (écoute active à retravailler), voix anglaise à améliorer.
- **Phase 5 — Packaging** ✅ Terminée le 2026-10-07 : `Jarvis.app` signée ad-hoc, `.pkg` vers /Applications, « Lancer à la connexion »,
  icônes, README d'installation. Réserve : test d'installation propre déclaré par l'utilisateur, non observé par Claude.
**V2 (en cours)** — Mot d'activation Mac (cf docs/specs/SPEC-2026-10-07-wakeword-mac.md)

## 8. Risks & Mitigations

- **Risque** : Whisper gère mal le changement de langue en milieu de phrase → **Mitigation** :
  tester en Phase 1 ; détection par segment, repli sur la langue de la phrase précédente.
- **Risque** : wake word par STT = CPU/batterie élevés, mal transcrit → **Mitigation** :
  VAD en amont ; migrer vers Porcupine/openWakeWord si dépassé.
- **Risque** : voix système FR/EN de qualité insuffisante → **Mitigation** : TTS premium en V2.
- **Risque** : modèle Ollama trop lent selon la RAM → **Mitigation** : choisir un modèle
  plus petit ; mesurer en Phase 1 ; basculer cloud si besoin.
- **Risque** : `.pkg` non notarisé bloqué par Gatekeeper → **Mitigation** : installation
  locale sans quarantaine, ou clic droit > Ouvrir ; notariser seulement si distribué.
- **Risque** : Claude Pro inutilisable comme backend (conditions Anthropic, non vérifié) →
  **Mitigation** : OpenRouter / API ; vérifier les conditions avant tout contournement.
