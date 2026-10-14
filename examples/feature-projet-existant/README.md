# Exemple : `/feature` sur un projet existant sans PRD

Cas : `factures-api`, une petite API Node/TypeScript écrite sans le kit (pas de `/brainstorm`, pas de `/architect`, pas de PRD). On veut y ajouter une feature : la relance par e-mail des factures impayées.

Ce dossier montre **ce que `/feature` laisse dans le projet à la fin** — il n'y a pas de code, seulement les artefacts :

| Fichier | Rôle |
|---------|------|
| `docs/features/2026-10-14-relance-factures-impayees.md` | Brief de cadrage (besoin, points d'intégration dans le code, risques, **section Sécurité**) |
| `PRD.md` | PRD **reconstitué** depuis le code : V1 « existant » cochée, V2 = la feature |
| `STATUS.md` | État minimal : phase active et prochaine action |
| `STRUCTURE.md` | Arborescence + marqueurs attendus par `/evoluer` |
| `CLAUDE.md` | Identité + Stack détectées dans le code (signalé à l'utilisateur avant écriture) |

Suite du cycle : `/close → /clear → /plan docs/features/2026-10-14-relance-factures-impayees.md → /execute`. `/plan` appelle ensuite `tool-security-gate` en mode plan sur les tâches sensibles (envoi d'e-mail, secret SMTP, requêtes SQL).
