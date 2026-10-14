<!--
SPEC pour une évolution. Frozen après /execute. Cap ~80 lignes.
-->

# SPEC — Mot d'activation « Mac »

> **Version cible** : V2
> **Créé le** : 2026-10-07
> **Status** : draft

## Feature

Remplacer le mot d'activation « Jarvis » par « Mac » pour l'écoute active. Whisper transcrit « Jarvis » en « Service » ou « Quel âge », ce que la liste d'alias de `WakeWordMatcher` ne rattrape pas (réserve de la Phase 4). Le nom de l'app (Jarvis) ne change pas : seul le mot déclencheur change. Usage perso, FR et EN.

**Critère de succès (1 ligne)** : sur 10 « Mac, quelle heure est-il ? » à voix normale, ≥ 9 déclenchent l'écoute active, avec 0 faux positif sur 2 min de bruit ou de parole.

## Examples

- **FR** : « Mac, ouvre Safari » → l'écoute active se déclenche, la commande « ouvre Safari » est exécutée.
- **EN** : « Mac, what's on my agenda? » → déclenchement, réponse en anglais.
- **Non-déclenchement** : « mon Mac est lent » ou une conversation sur un Mac → aucune réaction.

## Documentation

- `Sources/JarvisCore/WakeWordMatcher.swift` : matcher actuel (alias + distance d'édition).
- `docs/plans/phase-4-bench.md` § Limites : constat « Jarvis » mal transcrit.
- `Tests/JarvisCoreTests/WakeWordMatcherTests.swift`, `WakePipelineLiveTests.swift` : tests existants à adapter.

## Considerations

- **Risque** : mot de 3 lettres fragile ; le matching flou actuel (distance ≤ 1) accepterait « max », « mag », « mat ». Prévoir un matching strict et des alias mesurés, pas devinés.
- **Piège** : « Mac » est courant dans une phrase (« sur mon Mac »). La règle « dans les 3 premiers mots » peut ne pas suffire ; envisager le premier mot seulement.
- **Piège** : Whisper peut rendre « Mac » par « Mack », « Max », « Mark » ou « Mach » selon la langue.
- **Contrainte** : première tâche du plan = banc de transcription de « Mac » en FR et EN avant tout changement de code ; si le seuil ≥ 9/10 est hors d'atteinte, revenir au PRD § 4 (moteur wake word dédié).
- **Contrainte** : mettre à jour le libellé du menu (« Écoute active (« Jarvis ») ») et le README.
