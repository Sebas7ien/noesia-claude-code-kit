# Exemple : `/feature` sur un projet desktop existant sans PRD

Cas : `carnet-notes`, une petite application de notes de bureau (Tauri v2 : cœur Rust + interface web) écrite sans le kit : pas de `/brainstorm`, pas de `/architect`, pas de PRD. On veut y ajouter l'export des notes vers un dossier choisi par l'utilisateur.

Ce dossier montre **ce que `/feature` laisse dans le projet à la fin** : il n'y a pas de code, seulement les artefacts. Ce qui change par rapport à [`../feature-projet-existant/`](../feature-projet-existant/) (webapp) : `project_type` vaut `desktop` même si l'interface est web, l'Étape 1 de `/feature` lit les fichiers décrits dans `.claude/skills/feature/references/native-discovery.md` (`tauri.conf.json`, `capabilities/`, `Cargo.toml`), et le brief porte sur des surfaces propres au desktop (système de fichiers, commandes exposées à l'interface).

| Fichier | Rôle |
|---------|------|
| `docs/features/2026-10-14-export-notes-dossier.md` | Brief de cadrage : besoin, intégration (commande Rust, capability fs), risques, **section Sécurité** (chemin choisi par l'utilisateur, portée fs, écrasement) |
| `docs/security-checklist.md` | Règles sécurité du projet, créées **après** `/feature` (le brief disait « baseline seule ») |
| `PRD.md` | PRD **reconstitué** depuis le code : V1 « existant » cochée, V2 = la feature |
| `STATUS.md` | État minimal : phase active et prochaine action |
| `STRUCTURE.md` | Arborescence Tauri + marqueurs attendus par `/evoluer` |
| `CLAUDE.md` | Identité + Stack détectées dans le code, avec `ui_tests:` **par OS** (signalé à l'utilisateur avant écriture) |

Suite du cycle : `/close → /clear → /plan docs/features/2026-10-14-export-notes-dossier.md → /execute`. `/execute` build l'app, lance les tests de l'outil `ui_tests:` de chaque OS et prend des captures de la fenêtre, jamais avec Playwright. `/plan` appelle `tool-security-gate` en mode plan sur les tâches sensibles (commande d'export, portée fs).
