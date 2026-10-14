---
name: feature
description: Utiliser pour ajouter une feature à un projet EXISTANT qui n'a jamais eu /architect ni /brainstorm (pas de PRD.md, pas de STATUS.md). Lit le code pour reconstituer le contexte, cadre la feature par dialogue, écrit un brief dans docs/features/{date}-{slug}.md et crée les artefacts manquants (PRD, STATUS, STRUCTURE) pour que /plan et /execute fonctionnent. Ne PAS utiliser sur un projet neuf (→ /brainstorm puis /architect) ni sur un projet qui a déjà un PRD v2.2 (→ /evoluer ou /plan). N'opère aucun routing automatique.
---

# Skill /feature — cadrer une feature sur un projet existant sans PRD

## Pour quoi faire

Le projet existe (du code, un git log) mais n'est jamais passé par `/brainstorm` ni `/architect` : pas de PRD, pas de STATUS. `/plan` et `/evoluer` ne peuvent pas démarrer sans ces artefacts. `/feature` :

1. Détecte ce qui existe déjà.
2. Lit le code pour reconstituer stack, conventions et points d'intégration.
3. Cadre la feature par dialogue (3-5 questions).
4. Écrit le brief `docs/features/{date}-{slug}.md`.
5. Crée les artefacts manquants (PRD reconstitué, STATUS, STRUCTURE) après validation.
6. Propose le handoff — l'utilisateur choisit.

Aucune écriture avant validation explicite de l'utilisateur (diffs affichées d'abord).

## Étape 0 — détection (pas de routing automatique)

Vérifie l'existence de : `PRD.md`, `STATUS.md`, `STRUCTURE.md`, `CLAUDE.md`, `memory/decisions.md`, `docs/`.

- **`PRD.md` avec `## 7. Implementation Phases`** : signale-le et propose à l'utilisateur de continuer avec `/evoluer` (projet livré) ou `/plan` (phase en cours). Il choisit ; s'il insiste pour continuer `/feature`, ne recrée pas le PRD (saute 4a) et crée seulement ce qui manque.
- **`PRD.md` ancien format ou malformé** : STOP, demande quoi faire (ne jamais écraser).
- **Pas de PRD** : continue.

Pas de dépôt git → propose `git init` avant, ne l'exécute pas sans accord.

## Étape 1 — découverte du code (lecture seule)

Lis en parallèle ce qui existe : `package.json` / `pyproject.toml` / `build.gradle.kts` / `Package.swift` etc., configs (`tsconfig`, `next.config.*`, `docker-compose.yml`, `.mcp.json`), `.env.example`, arborescence (2-3 niveaux), `README`, `git log --oneline -20`, présence de tests.

Extrais :
- **Stack réelle** et `project_type` (webapp / site / desktop / mobile / automation).
- **Conventions observées** : nommage, gestion d'erreurs, typage, emplacement des tests.
- **Livré ou non** : déploiement visible (workflow CI de deploy, URL dans README, config hosting).
- **Points d'intégration probables** pour la feature (fichiers/modules à toucher), une fois la feature connue.

**Projet natif** (`mobile` / `desktop` : `Package.swift`, `*.xcodeproj`, `build.gradle*`, `AndroidManifest.xml`, `src-tauri/`, Electron…) : lis `references/native-discovery.md` pour savoir quels manifestes, permissions et outils `ui_tests:` examiner. Pas de Playwright pour une app native.

Si le projet est gros (> ~5 fichiers à lire en profondeur), propose de déléguer à `research-delegate` pour garder le contexte propre. Pas de scan exhaustif.

Résume en 5-8 lignes ce que tu as compris du projet et demande confirmation avant de poursuivre.

## Étape 2 — cadrage de la feature (3 à 5 questions, une par une)

Reformule d'abord : *"Si je comprends bien, tu veux **{feature}** dans **{projet}**. C'est ça ?"*

Puis pioche (sans reposer ce qui est déjà dit) :
1. **Pour qui / pour quoi** — problème concret, exemple d'usage récent.
2. **Critère de succès** — 1 phrase concrète et vérifiable.
3. **Intégration** — où ça se greffe (UI, API, données) ; propose les points repérés à l'Étape 1.
4. **Contraintes** — deadline, stack imposée, services externes, compat existant.
5. **Ampleur** — S / M / L ; si L, propose de découper en plusieurs features.

Capacité technique nouvelle détectée (n8n, Stripe, Google Drive, email transactionnel, etc.) et absente de la stack : signale-la comme risque dans le brief ; l'installation sera traitée par `/plan` / `/evoluer`.

**Surface sécurité** : repère si la feature touche une surface sensible (route non publique, auth/autorisation, upload, SQL, secret/clé, canal externe/webhook, prompt LLM, données personnelles). Si oui, pose une question dédiée : qui peut l'utiliser, quelles données sont exposées, quel secret ou service tiers est impliqué. Pour un projet natif, les surfaces propres à la plateforme (permission OS, système de fichiers, pont front ↔ natif, deep links…) sont listées dans `references/native-discovery.md`. Vérifie aussi si `docs/security-checklist.md` existe ; sinon note « baseline seule » dans le brief et suggère de la créer (ne la crée pas toi-même).

Niveau de rigueur : **STANDARD** par défaut (ne pose pas la question). L'utilisateur peut demander LITE ou FULL de lui-même.

## Étape 3 — brief

`mkdir -p docs/features`. Fichier `docs/features/{YYYY-MM-DD}-{slug}.md` (slug kebab-case 3-5 mots ; en cas de collision, suffixe `-02`).

```markdown
# Feature : {nom}

## En 1 phrase
{description}

## Contexte projet (reconstitué)
- Stack : {…} · type : {project_type} · livré : {oui/non/inconnu}
- Conventions : {nommage, erreurs, tests}

## Besoin
- Pour qui / pour quoi : {…}
- Critère de succès : {…}

## Intégration dans le code
- {fichier/module} — {ce qui change}

## Contraintes & dépendances
- {…ou "aucune connue"}

## Risques
- {risque + mitigation envisagée}

## Ampleur
{S | M | L}

## Sécurité
- Surfaces sensibles touchées : {liste, ou "aucune"}
- Données / secrets / services tiers impliqués : {…}
- Checklist projet : {docs/security-checklist.md présent | absente → baseline seule}

## Hypothèses à valider
- [ ] {…}
```

## Étape 4 — artefacts manquants (après validation)

Affiche d'abord la liste et le contenu proposé de chaque fichier, attends un « oui ». Ne crée **que ce qui manque** ; n'écrase jamais.

### 4a — `PRD.md` (format v2.2, 8 sections, cap 100 lignes)

Si `templates/PRD-template.md` existe dans le projet, suis-le. Sinon, structure de repli, dans cet ordre :

1. **Vision** — 1-3 phrases (déduites du README/code, confirmées par l'utilisateur).
2. **Personas** — 1-3 courts.
3. **Scope actuel (V_n)** — `### Core` / `### Technique` : fonctionnalités existantes déduites du code en `[x]`, la nouvelle feature en `[ ]`.
4. **Hors scope (différé)** — checkboxes, ou « rien identifié ».
5. **Constraints non-négociables**.
6. **Success Criteria** — dont le critère de succès de la feature.
7. **Implementation Phases** — `**V1 (existant, reconstitué)** — état avant /feature` puis `**V2 (en cours)** — {feature} (cf docs/features/{date}-{slug}.md)`.
8. **Risks & Mitigations**.

Marque le PRD comme reconstitué (ligne d'en-tête) pour que l'utilisateur sache qu'il est à relire. Les marqueurs `**V_N` doivent être présents (`/evoluer` les parse).

### 4b — `STATUS.md`
Minimal : phase active (V2 — {feature}), dernier commit, prochaine action (`/plan …`).

### 4c — `STRUCTURE.md`
Arborescence courte + `<!-- structure:integrations -->` + `<!-- structure:key-files -->` + `<!-- structure:evolutions-summary -->` (marqueurs attendus par `/evoluer`).

### 4d — `CLAUDE.md` du projet
**Signale avant de toucher** (fichier de config). Si absent ou sans `## Stack`, propose d'ajouter la stack détectée et `project_type`. Si le projet est déjà en prod, propose d'ajouter `<!-- ship:url -->{url}` (sinon ne le mets pas).

### 4e — `memory/decisions.md`
Seulement si la feature implique un choix d'architecture (nouveau provider, pattern, changement de stack) : ADR-001 (ou suivant) au format `Status / Date / Context / Decision / Consequences`. Sinon rien.

## Étape 5 — handoff explicite

```
✅ /feature terminé :
   - docs/features/{date}-{slug}.md
   - créés : {PRD.md, STATUS.md, STRUCTURE.md …}

Étapes suivantes pour repartir propre :
  1. /close   → commit + STATUS.md
  2. /clear   → contexte vide
  3. /plan docs/features/{date}-{slug}.md   → découper en tâches
  4. /execute

Alternative : /evoluer docs/features/{date}-{slug}.md si tu veux un SPEC daté
(le projet est livré et tu veux la cérémonie complète). C'est toi qui choisis.
```

Ne route PAS automatiquement. Ne commit pas (la règle globale l'interdit sans demande ; `/close` s'en charge).

## Règles strictes

- Jamais d'écriture sans validation ; jamais d'écrasement d'un fichier existant.
- Jamais de routing automatique vers `/plan` ou `/evoluer`.
- Le PRD reconstitué reste un brouillon déduit du code : dis-le clairement, ne l'invente pas — en cas de doute sur ce que fait le code, pose la question.
- Cap 100 lignes sur le PRD.
- Pas de dépendance ajoutée sans la signaler.
- La passe sécurité elle-même n'est pas faite ici : `/plan` appelle `tool-security-gate` en mode plan et `/execute` en mode gate. `/feature` ne fait que repérer les surfaces sensibles pour les leur transmettre via le brief.

## Quand ne PAS utiliser

- Projet neuf, idée floue → `/brainstorm` puis `/architect`.
- Projet avec PRD v2.2 → `/evoluer` (livré) ou `/plan` (en cours).
- Modifier une feature existante → édition manuelle.
- Refonte complète → `/architect`.

## Trace de fin

Avant le handoff, append une ligne à `tmp/skill-trace.jsonl` (créer `tmp/` si absent) :

```json
{"skill": "feature", "artifact": "docs/features/{YYYY-MM-DD}-{slug}.md", "next": "user-choice", "ts": "<ISO8601 UTC>"}
```

**Prochaine étape** : choix utilisateur entre `/plan` et `/evoluer` avec le brief en argument, via `/close → /clear`.
