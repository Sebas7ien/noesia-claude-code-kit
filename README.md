# Noesia Claude Code Kit

> **Framework guidé Claude Code pour débutants**, du démarrage au scaling. Supporte 3 cas d'usage : **site vitrine**, **web app SaaS**, **automatisation n8n**. Tape `/start` après le clone, suis le guide piloté, et tu attaques ton premier projet en 5 minutes.

## Pourquoi ce kit existe

Claude Code seul, c'est un assistant qui répond à ta dernière question. Avec ce kit, c'est un **chef de projet** qui :

- Te pose les bonnes questions au bon moment (`/start`, `/architect`)
- Découpe le travail en phases livrables (`/plan`)
- Refuse de dire "ça devrait marcher" — vérifie pour de vrai (`/validate`, `/livrer`)
- Tient à jour un journal de bord entre sessions (`STATUS.md`, `MEMORY.md`, maintenus par `/close`)
- Te dit où tu en étais quand tu reprends le lendemain (`/prime`)

Concrètement : tu passes de "je code une feature au feeling pendant 4h" à "je ship une phase en 1h30 avec un PRD, un plan challengé, un test Playwright qui passe, et un commit propre".

## Pour qui

- **Freelance ou consultant solo** qui code régulièrement et veut sortir de l'improvisation session-par-session
- **Entrepreneur no-code/low-code** qui veut shipper des webapps ou des automatisations n8n sans demander à un dev à chaque feature
- **Dev junior** qui veut apprendre la discipline du PRD → plan → exécution → validation sans passer 6 mois en équipe

Pas pour : équipes de 5+ devs (le kit assume un single-author flow), projets greenfield à 0 contexte technique (tu dois savoir cliquer dans Vercel/Supabase/n8n), missions ultra-courtes (< 2h, le kit a un overhead de cadrage qui ne se rembourse pas).

## Démarrer maintenant

Lis [`QUICKSTART.md`](QUICKSTART.md) (30 secondes). Tu seras opérationnel.

## Comprendre le kit en profondeur

Lis [`docs/KIT.md`](docs/KIT.md). Méta-doc complète : tous les skills, parcours détaillés, installation MCP, conventions.

## 3 parcours typiques

**Parcours 1 — Création (premier projet)** :
```
/start              ← cadrage + outillage + project_type + Request Classification
   ↓
/brainstorm         ← (optionnel) si idée floue
   ↓
/architect          ← PRD.md + providers favoris + Étape 6 scaffold + provisioning
   ↓
/design             ← SI webapp : produit DESIGN.md (sinon skip)
   ↓
/plan Phase 1       ← découpe en tâches (adapté project_type)
   ↓
/challenge          ← (optionnel, systématique en FULL)
   ↓
/execute            ← coche les [x] une par une
   ↓
/validate           ← verdict réel "ça marche / ça marche pas"
   ↓
/close              ← MANDATORY : ✅ Terminée + commit + harvest mémoire
   ↓
/plan Phase 2 → ... (boucle)
   ↓
/livrer             ← deploy prod selon ## Stack (Vercel/Netlify/Cloudflare/autre)
```

**Parcours 2 — Reprise (rituel d'entrée de session, après pause ou absence)** :
```
/prime              ← lit PRD + STRUCTURE.md + plans + git log + MEMORY.md → propose la suite
   ↓
{action proposée}   ← /execute, /plan, /livrer, /evoluer selon l'état détecté
```

**Parcours 3 bis — Projet existant sans PRD (tu veux y ajouter une feature)** :
```
/feature            ← lit le code + cadre la feature + crée PRD/STATUS/STRUCTURE manquants
   ↓
/close → /clear
   ↓
/plan docs/features/{date}-{slug}.md   (ou /evoluer si le projet est livré)
   ↓
/execute → /validate → /close
```

**Parcours 3 — Évolution (projet livré, tu veux ajouter une feature)** :
```
/prime              ← détecte projet livré → propose /evoluer
   ↓
/evoluer            ← parse PRD + 3 questions cadrage + insère Phase N+1
   ↓
/plan Phase N+1     ← reprend le flux standard
   ↓
/execute → /validate → /close → /livrer
```

## Démarrage

**Recommandé — bouton "Use this template"** (historique git propre dès le départ) :

1. Va sur https://github.com/Sebas7ien/noesia-claude-code-kit
2. Clique le bouton vert **"Use this template"** → **"Create a new repository"**
3. Choisis le nom de ton projet → "Create repository"
4. Clone ton nouveau repo :
   ```bash
   git clone https://github.com/TON-USERNAME/TON-PROJET.git
   cd TON-PROJET
   claude
   ```

**Alternative — clone direct du kit** (si tu veux contribuer ou si l'option template n'est pas dispo) :

```bash
git clone https://github.com/Sebas7ien/noesia-claude-code-kit.git mon-projet
cd mon-projet
claude
```

`/start` détectera le clone direct et te proposera automatiquement le bootstrap (reinit `.git` + remote `upstream` pour les futures updates + commit initial) en une seule question oui/non.

Une fois dans Claude, tape :

```
/start
```

`/start` te guide :
1. **Bootstrap** (si clone direct du kit) — reinit historique git + remote `upstream` + commit initial, en une question oui/non
2. **Détection projet** — lit MEMORY.md + CLAUDE.md + PRD.md. Si projet existant détecté → bifurque vers `/prime`.
3. **Visite du kit** (skippable)
4. **3 questions de cadrage** → remplit la section `## Identité` de ton `CLAUDE.md` + écrit `project_type` ∈ `{webapp, site, automation, desktop, mobile}`
5. **Sécurisation des credentials** → `.env` créé et gitignored, `.mcp.json` avec syntaxe `${VAR}`
6. **Vérification de l'outillage** → Playwright + n8n MCP + plugin frontend-design
7. **Routage** → `/brainstorm` (idée floue) ou `/architect` (idée claire) ou `/prime` (projet existant)

Sortie : projet cadré, outillage testé, prochain skill suggéré.

## Skills

**Table principale — 10 commandes du cycle de vie projet** *(certaines arrivent en v2.0.0 GA — voir colonne "Statut")* :

| Skill | Rôle | Statut |
|-------|------|--------|
| `/start` | Onboarding piloté (5 phases + détection projet existant qui bifurque vers `/prime`). À taper 1x à l'ouverture. Écrit la variable `project_type` ∈ {webapp, site, automation, desktop, mobile}. | ✅ |
| `/brainstorm` | Clarifier une idée vague en 3 questions. Route 2 délègue à `research-delegate` pour explorer projets similaires. | ✅ |
| `/architect` | Produire un `PRD.md` structuré + **Étape 2b demande les providers favoris** (hosting/BDD/email) avant de figer la stack + **Étape 6 Provisioning & Scaffold** (scaffold le repo selon `project_type` + providers retenus + écriture `.env`). Écrit la section `## Stack` du `CLAUDE.md`. | ✅ |
| `/design` *(webapp uniquement)* | **Définit** le design system au format **DESIGN.md officiel Google** (open-source, spec alpha — YAML tokens + 8 sections markdown). Template fourni dans `.claude/skills/design/template.md`. **Complémentaire** au plugin Anthropic `frontend-design` qui lit `DESIGN.md` pour build des composants cohérents. | ✅ |
| `/plan` | Découper UNE phase du PRD en tâches avec critères "Fait quand". Lit `DESIGN.md` si la phase touche à l'UI. Adapte ses questions selon `project_type`. | ✅ |
| `/execute` | Exécuter le plan tâche par tâche, cocher au fur et à mesure. Délègue à `research-delegate` si bloqué par une doc API externe. | ✅ |
| `/validate` | Vérifier que la phase marche pour de vrai (Playwright / n8n / autre / **audit policy d'accès BDD** si données clients). Jamais "ça devrait marcher". | ✅ |
| `/close` | Clôturer la phase : marque ✅ Terminée dans le PRD + commit conventionnel + harvest learnings + handoff. **Mandatory** après `/validate ✅`. | ✅ |
| `/livrer` | Déployer en production selon `## Stack` (hosting/BDD/email **détectés depuis CLAUDE.md, jamais hardcode** — Vercel/Netlify/Cloudflare/GitHub Pages/autre) + checklist policy d'accès advisory + smoke test. | ✅ |
| `/evoluer` | Ajouter une nouvelle feature à un projet livré : insère Phase N+1 dans PRD existant sans écraser (regex parse + 3 questions + idempotent). | ✅ |
| `/feature` | Ajouter une feature à un projet **existant sans PRD** (jamais passé par `/architect`) : lit le code, cadre la feature, écrit `docs/features/{date}-{slug}.md` et crée les artefacts manquants (PRD reconstitué, STATUS, STRUCTURE). Aucun routing automatique. | ✅ |

**Skills optionnels avancés** :

| Skill | Rôle |
|-------|------|
| `/challenge` | Devil's advocate sur un plan : 3 risques + 3 hypothèses non vérifiées + verdict GO/REWORK/STOP. Systématique en Request Classification FULL. |
| `tool-security-gate` | Passe sécurité appelée par `/plan` (critères `Sécurité :`) et `/execute` (verdict en fin de phase). Voir « Passe sécurité » ci-dessous. |
| `/security-watch` | Veille CVE et audit des dépendances contre les bases publiques. Déclenchement manuel. |

**Notes hors table** :
- Tu commences une session sur un projet existant ? Tape `/prime` (couvre aussi la reprise après absence) — lit `PRD.md` + `STRUCTURE.md` + plans (`docs/plans/` priorité, fallback `plans/` puis racine) + git log + `MEMORY.md` et propose 1-3 actions concrètes. `/start` détecte automatiquement les projets existants et bifurque vers `/prime`.
- Pour debugger un bug → tape `/debug` (built-in Claude Code natif). **Règle de comportement** : écris d'abord un test de régression qui reproduit le bug, puis fais-le passer (TDD).
- **Mémoire persistante** : `/close` maintient automatiquement `memory/learnings/`, `memory/topics/`, `memory/decisions.md` et `MEMORY.md` (index racine) à chaque clôture de phase, via 3 questions ciblées (décision arch ? gotcha ? pattern ?). **L'utilisateur n'édite jamais ces fichiers à la main.** Détails dans le template `CLAUDE.md` section `## Mémoire persistante`.

Workflow type : `/start` → `/brainstorm`? → `/architect` (PRD + providers + scaffold) → `/design`? → `/plan` Phase 1 → `/execute` → `/validate` → `/close` → `/plan` Phase 2 → ... → `/livrer`. Reprise : `/prime`. Évolution : `/evoluer` (projet avec PRD) ou `/feature` (projet existant sans PRD).

`/design` est conditionnel — skip si `project_type` ∈ {site, automation, desktop, mobile} (pour desktop/mobile : `design-ui-proposal`). `/challenge` est optionnel.

## Sous-agent

`research-delegate` (`.claude/agents/`) — sous-agent read-only invoqué automatiquement par les skills (recherche web pour `/brainstorm`, scout codebase pour `/plan`, lecture doc pour `/execute`, parallélisation pour `/validate`). Lit jusqu'à 15 sources et renvoie 3-10 bullets. Garde ta fenêtre de contexte propre.

## Skills n8n

7 skills officiels de [czlonkowski/n8n-skills](https://github.com/czlonkowski/n8n-skills) (MIT) dans `.claude/skills/n8n/` : `n8n-mcp-tools-expert`, `n8n-workflow-patterns`, `n8n-validation-expert`, `n8n-node-configuration`, `n8n-expression-syntax`, `n8n-code-javascript`, `n8n-code-python`. Auto-invoqués quand tu touches à n8n.

## Règles path-scoped

`.claude/rules/` contient des fichiers Markdown avec frontmatter `paths:` qui se chargent **automatiquement** quand Claude ouvre un fichier matching le glob. Permet de garder le `CLAUDE.md` court.

Exemples fournis :
- `frontend.md` (`paths: src/**/*.{ts,tsx}`) — React + Tailwind + shadcn
- `n8n.md` (`paths: **/*.workflow.json`) — naming workflows, credentials, webhooks, anti-patterns

Voir `.claude/rules/README.md` pour le détail du pattern.

## Template `CLAUDE.md`

5 couches **Identité / Stack / Conventions / Instructions / Contexte métier** avec ancres HTML pour les zones écrites par les skills (`<!-- start:identité -->`, `<!-- architect:stack -->`) + 4 règles de comportement (réfléchir avant de coder, simplicité, modifs chirurgicales, exécution orientée but) + section "Comment ce CLAUDE.md est entretenu" (séquencement skills, qui écrit quoi, déportation vers `.claude/rules/`) + section "Sécurité des credentials".

## Exemples remplis

Huit exemples : un par `project_type`, plus trois exemples de `/feature` sur projet existant (webapp, mobile, desktop) :

| Exemple | `project_type` | Niveau | Stack |
|---------|---------------|--------|-------|
| [`examples/site-vitrine-coach/`](examples/site-vitrine-coach/) | `site` | LITE | Next.js + Vercel + Resend |
| [`examples/webapp-saas-freelance-devis/`](examples/webapp-saas-freelance-devis/) | `webapp` | STANDARD | Next.js + Supabase + n8n + Resend + Vercel |
| [`examples/automation-n8n-veille-rss/`](examples/automation-n8n-veille-rss/) | `automation` | STANDARD | n8n + Supabase + Anthropic Haiku + Slack |
| [`examples/desktop-jarvis-macos/`](examples/desktop-jarvis-macos/) | `desktop` | STANDARD | Swift + SwiftUI + WhisperKit + Ollama |
| [`examples/mobile-suivi-habitudes/`](examples/mobile-suivi-habitudes/) | `mobile` | STANDARD | SwiftUI + Compose, stockage local, Maestro |
| [`examples/feature-projet-existant/`](examples/feature-projet-existant/) | `webapp` (existant) | STANDARD | Node + TypeScript + PostgreSQL |
| [`examples/feature-mobile-existant/`](examples/feature-mobile-existant/) | `mobile` (existant) | STANDARD | Kotlin + Compose + Room, Maestro |
| [`examples/feature-desktop-existant/`](examples/feature-desktop-existant/) | `desktop` (existant) | STANDARD | Tauri 2 (Rust + Svelte), WebdriverIO |

Détails et critères de choix dans [`examples/README.md`](examples/README.md). Ouvre ces fichiers en parallèle de tes propres essais — le format est plus parlant qu'une longue doc.

📖 **Tuto narratif "session réelle de A à Z"** : [`examples/webapp-saas-freelance-devis/SESSION.md`](examples/webapp-saas-freelance-devis/SESSION.md). Tu lis les commandes que je tape, les réponses Claude tronquées aux essentiels, du clone initial à la prod en 3h20. Utile si tu veux comprendre **comment les skills s'enchaînent réellement** avant de te lancer.

## Scaffolding outillage

- `.mcp.json` vide prêt à recevoir Playwright, n8n MCP, plugin frontend-design (commandes documentées dans `CLAUDE.md`)
- `.env.example` avec placeholders pour n8n, Anthropic SDK, Supabase, Resend
- `.gitignore` durci sur `.env`, `.env.local`, `.env.*.local`, `.envrc`
- `tmp/` (gitignored sauf `.gitkeep`) pour les fichiers temporaires : screenshots Playwright produits par l'auto-évaluation (cf. CLAUDE.md règle 6), dumps debug, outputs intermédiaires de skills. **Nettoie après usage** — le dossier existe pour ne pas polluer le repo, pas pour s'accumuler.
- `memory/` (structure créée à l'init) + `MEMORY.md` (index racine) — **maintenu par `/close`**, pas d'édition manuelle

## Passe sécurité (`tool-security-gate`)

Un seul point d'entrée pour la sécurité dans le cycle `/plan` → `/execute`. Rien n'est lancé à la main : les deux skills l'appellent quand il le faut.

| Quand | Mode | Ce que ça fait |
|-------|------|----------------|
| `/plan` Étape 3.5, si au moins une tâche touche une surface sensible (route non publique, auth, upload, SQL, secret, canal externe, prompt LLM) | **plan** | Ajoute une ligne `Sécurité :` sous les tâches concernées, avec des critères vérifiables (une commande ou une lecture de fichier). Ne crée pas de tâche. |
| `/execute` Étape 2.6, une seule fois en fin de phase | **gate** | Applique la checklist au diff de la phase, lance `security-review`, et `security-watch` seulement si un manifest de dépendances a changé. Rend un verdict. |

**Verdict** : ❌ FAIL (au moins un bloquant : secret en clair, SQL concaténé, route sans auth, webhook sans signature) interdit le passage à `/validate` ; ⚠️ WARN (mineurs) ne bloque pas ; ✅ PASS. La ligne « Non vérifié » signale ce qui n'a pas pu être examiné : ce n'est pas un PASS silencieux.

**Règles appliquées** : la *baseline* du kit (toujours) + `docs/security-checklist.md` du repo (si elle existe, règles propres au projet). Le kit ne la crée jamais seul : `/feature` ou le gate te suggèrent de l'écrire, tu décides. Exemple : [`examples/feature-projet-existant/docs/security-checklist.md`](examples/feature-projet-existant/docs/security-checklist.md).

**Ne pas confondre** : `security-watch` = veille externe (CVE, audit de dépendances), déclenchée à la main ; `security-review` = relecture générale d'un diff ; `tool-security-gate` = checklist appliquée à une phase.

## `/feature` ou `/evoluer` ?

Les deux ajoutent une feature à un projet qui existe déjà. Seule change la présence d'un PRD :

| Ton projet… | Skill |
|-------------|-------|
| n'a jamais eu de PRD (code hérité, projet hors kit, prototype) | `/feature` |
| a un PRD au format du kit et est livré | `/evoluer` |
| a un PRD et une phase en cours | `/plan` directement |
| est neuf, l'idée est floue | `/brainstorm` puis `/architect` |

`/feature` lit le code, pose 3 à 5 questions (dont une sur la surface sécurité), écrit un brief `docs/features/{date}-{slug}.md`, puis crée après validation les artefacts manquants (PRD reconstitué, STATUS, STRUCTURE). Il ne route jamais automatiquement : tu enchaînes `/close → /clear → /plan docs/features/{date}-{slug}.md`. Une fois le PRD reconstitué, les features suivantes passent par `/evoluer`. Exemple complet : [`examples/feature-projet-existant/`](examples/feature-projet-existant/).

## Sécurité

### Credentials

**Règle non-négociable** : aucune clé API en clair dans un fichier committé.

Pattern Anthropic-officiel (appliqué automatiquement par `/start`) :
- `.env` (gitignored) → vraies valeurs
- `.env.example` (committé) → placeholders pour la doc
- `.mcp.json` (committé) → syntaxe `${VAR}` :
  ```json
  { "env": { "N8N_API_KEY": "${N8N_API_KEY}" } }
  ```
- Charger `.env` dans le shell avant `claude` : `set -a && source .env && set +a` (ou installer `direnv` pour le faire automatiquement)

### Row-Level Security (Supabase)

Dès que ton app contient des données clients (nom, email, téléphone, SIRET, transcripts RDV, devis, factures, leads, multi-tenant), **RLS dès le premier deploy. Sans exception.**

Cas inverse : un outil à données éphémères sans PII (sondage live, kanban temporaire) peut skipper RLS. Mais c'est minoritaire.

> Test simple : si le pire scénario d'une fuite est "rien de grave", tu peux skipper. Sinon, RLS systématique avant prod.

Et au-delà de RLS : valide les inputs côté serveur (jamais juste côté client), ne committe **jamais** `.env`.

## 📚 Pour en savoir plus

- [`docs/KIT.md`](docs/KIT.md) — Doc de référence complète du kit (skills, parcours, MCP install, sous-agent)
- [`CLAUDE.md.template`](CLAUDE.md.template) — Template projet (copié vers `CLAUDE.md` par `/start`, `CLAUDE.md` est gitignored)
- [`memory/README.md`](memory/README.md) — Système mémoire persistante (maintenu par `/close`)
- [`.claude/skills/`](.claude/skills/) — Détail de chaque skill (`/start` comme point d'entrée)
- [`.claude/rules/README.md`](.claude/rules/README.md) — Règles path-scoped auto-chargées

## Crédits

- **Skills n8n** : [czlonkowski/n8n-skills](https://github.com/czlonkowski/n8n-skills) (MIT, attribution dans `.claude/skills/n8n/LICENSE-czlonkowski`)

## Contribuer

Tu utilises le kit et tu vois un truc à améliorer ? Voir [`CONTRIBUTING.md`](CONTRIBUTING.md). Issues bienvenues, PRs encouragées (le kit a un bus factor = 1 aujourd'hui — toute contribution externe le rend plus solide).

## License

[MIT](LICENSE) — Copyright 2026 Brice Gachadoat. Fais-en ce que tu veux.
