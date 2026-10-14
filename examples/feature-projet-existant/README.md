# Exemple : `/feature` sur un projet existant sans PRD

Cas : `factures-api`, une petite API Node/TypeScript écrite sans le kit (pas de `/brainstorm`, pas de `/architect`, pas de PRD). On veut y ajouter une feature : la relance par e-mail des factures impayées.

Ce dossier montre **ce que `/feature` laisse dans le projet à la fin** — il n'y a pas de code, seulement les artefacts :

| Fichier | Rôle |
|---------|------|
| `docs/features/2026-10-14-relance-factures-impayees.md` | Brief de cadrage (besoin, points d'intégration dans le code, risques, **section Sécurité**) |
| `PRD.md` | PRD **reconstitué** depuis le code : V1 « existant » cochée, V2 = la feature |
| `STATUS.md` | État minimal : phase active et prochaine action |
| `STRUCTURE.md` | Arborescence + marqueurs attendus par `/evoluer` |
| `docs/security-checklist.md` | Checklist sécurité du projet, **créée après `/feature`** (le brief disait « baseline seule »). Lue par `tool-security-gate`. |
| `CLAUDE.md` | Identité + Stack détectées dans le code (signalé à l'utilisateur avant écriture) |

Suite du cycle : `/close → /clear → /plan docs/features/2026-10-14-relance-factures-impayees.md → /execute`. `/plan` appelle ensuite `tool-security-gate` en mode plan sur les tâches sensibles (envoi d'e-mail, secret SMTP, requêtes SQL).

## Ce que la passe sécurité donne sur cet exemple

La section `Sécurité` du brief liste trois surfaces : envoi d'e-mail à des tiers, secret SMTP/API, SQL. `/plan` les transmet à `tool-security-gate` (mode **plan**), qui ajoute une ligne `Sécurité :` sous les tâches concernées. Exemple de ligne produite :

```
- [ ] **3. Job quotidien `sendReminders()`** — Sélectionne les factures échues depuis ≥ 7 jours, envoie un e-mail, marque `reminded_at`. **Fait quand** : `npm test` vert et un seul e-mail part par facture, même si le job tourne deux fois.
  **Sécurité :** `reminded_at` est écrit dans la même transaction que la sélection ; la clé du service d'e-mail vient de `process.env` (`grep -rn "re_" src/` ne retourne rien).
```

En fin de phase, `/execute` lance le mode **gate** sur le diff. Exemple de verdict (fictif) :

```
## Verdict
⚠️ WARN

## Bloquants
- aucun

## Mineurs
- src/mail/client.ts:14 — pas de limite de taille sur le corps du message — `const body = template(data)`

## Non vérifié
- Envoi réel via le service d'e-mail (testé uniquement avec un faux serveur SMTP)
```

Restitution dans `/execute` : `Sécurité : anomalies mineures — 1 (à traiter hors phase)`. Un ❌ FAIL (secret en clair, SQL concaténé, route sans auth) bloque le passage à `/validate` ; un ⚠️ WARN ne bloque pas.
