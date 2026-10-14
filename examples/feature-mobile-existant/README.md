# Exemple : `/feature` sur un projet mobile existant sans PRD

Cas : `liste-courses`, une petite app Android (Kotlin + Jetpack Compose + Room) écrite sans le kit : pas de `/brainstorm`, pas de `/architect`, pas de PRD. On veut y ajouter un rappel quotidien par notification locale.

Ce dossier montre **ce que `/feature` laisse dans le projet à la fin** : il n'y a pas de code, seulement les artefacts. Ce qui change par rapport à [`../feature-projet-existant/`](../feature-projet-existant/) (webapp) : l'Étape 1 de `/feature` lit les fichiers natifs décrits dans `.claude/skills/feature/references/native-discovery.md` (`build.gradle.kts`, `AndroidManifest.xml`, `androidTest/`, `.maestro/`), et la section Sécurité du brief porte sur des surfaces propres à la plateforme.

| Fichier | Rôle |
|---------|------|
| `docs/features/2026-10-14-rappel-quotidien-courses.md` | Brief de cadrage : besoin, intégration (manifeste, WorkManager), risques, **section Sécurité** (permission de notification, récepteur, écran verrouillé) |
| `docs/security-checklist.md` | Règles sécurité du projet, créées **après** `/feature` (le brief disait « baseline seule ») |
| `PRD.md` | PRD **reconstitué** depuis le code : V1 « existant » cochée, V2 = la feature |
| `STATUS.md` | État minimal : phase active et prochaine action |
| `STRUCTURE.md` | Arborescence Android + marqueurs attendus par `/evoluer` |
| `CLAUDE.md` | Identité + Stack détectées dans le code, avec `ui_tests:` (signalé à l'utilisateur avant écriture) |

Suite du cycle : `/close → /clear → /plan docs/features/2026-10-14-rappel-quotidien-courses.md → /execute`. Chaque tâche UI du plan nomme son flow Maestro ou son test Compose ; `/execute` vérifie sur émulateur avec captures, jamais avec Playwright. `/plan` appelle `tool-security-gate` en mode plan sur les tâches sensibles (permission, récepteur de redémarrage).
