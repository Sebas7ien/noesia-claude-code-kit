# Exemples

Huit exemples remplis : un par `project_type` (`site`, `webapp`, `automation`, `desktop`, `mobile`) plus trois exemples de `/feature` sur un projet existant (webapp, mobile, desktop). Le kit s'adapte à ton cas — ces exemples sont là pour te montrer **le format attendu**, pas pour t'imposer une stack.

> 📖 **Tu veux voir une session réelle de A à Z ?** Lis [`webapp-saas-freelance-devis/SESSION.md`](webapp-saas-freelance-devis/SESSION.md) — tuto narratif avec les commandes tapées + sorties LLM abrégées, du `claude` initial à la mise en prod en 3h20 cumulées sur 2 jours. Le format est plus parlant qu'une doc abstraite.

## Comparaison rapide

| Exemple | `project_type` | Niveau Request Classification | Stack | Complexité | Skills utilisés |
|---------|---------------|------------------------------|-------|------------|-----------------|
| [`site-vitrine-coach/`](site-vitrine-coach/) | `site` | **LITE** (PRD 3 sections, 1 phase) | Next.js + Vercel + Resend | ⭐ | `/start` `/architect` `/plan` `/execute` `/validate` `/close` `/livrer` |
| [`webapp-saas-freelance-devis/`](webapp-saas-freelance-devis/) | `webapp` | **STANDARD** (PRD complet, 4 phases) | Next.js + Supabase + n8n + Resend + Vercel | ⭐⭐⭐ | tous + `/design` (DESIGN.md inclus) |
| [`automation-n8n-veille-rss/`](automation-n8n-veille-rss/) | `automation` | **STANDARD** (PRD complet, 2 phases) | n8n + Supabase + Anthropic Haiku + Slack | ⭐⭐ | tous sauf `/design` (pas d'UI) |
| [`desktop-jarvis-macos/`](desktop-jarvis-macos/) | `desktop` | **STANDARD** (PRD complet, 5 phases + V2) | Swift + SwiftUI + WhisperKit + Ollama, `.pkg` | ⭐⭐⭐ | tous sauf `/design` ; `/evoluer` (SPEC inclus) |
| [`mobile-suivi-habitudes/`](mobile-suivi-habitudes/) | `mobile` | **STANDARD** (PRD complet, 4 phases) | SwiftUI + SwiftData (iOS pilote), Compose + Room (Android), Maestro | ⭐⭐⭐ | tous sauf `/design` ; `design-ui-proposal` |
| [`feature-projet-existant/`](feature-projet-existant/) | `webapp` (existant) | **STANDARD** | Node + TypeScript + Express + PostgreSQL | ⭐⭐ | `/feature` puis `/plan` |
| [`feature-mobile-existant/`](feature-mobile-existant/) | `mobile` (existant) | **STANDARD** | Kotlin + Compose + Room, Compose UI tests + Maestro | ⭐⭐ | `/feature` puis `/plan` |
| [`feature-desktop-existant/`](feature-desktop-existant/) | `desktop` (existant) | **STANDARD** | Tauri 2 (Rust + Svelte), WebdriverIO + tauri-driver | ⭐⭐ | `/feature` puis `/plan` |

## Quel exemple regarder selon ton projet ?

- **Tu veux faire un site web simple** (présence en ligne, 1-5 pages, formulaire contact) → `site-vitrine-coach/`. Tu verras qu'on **skip `/design`**, qu'on choisit le niveau **LITE** pour aller vite, et que le PRD tient en 3 sections.
- **Tu veux faire une app web SaaS** (auth + BDD + utilisateurs) → `webapp-saas-freelance-devis/`. C'est l'exemple le plus riche : tu verras `DESIGN.md` rempli (palette + 9 composants), `phase-1-plan.md` avec 5 tâches détaillées, et le PRD complet 7 sections.
- **Tu veux faire une automatisation** (workflow déclenché, pas d'UI) → `automation-n8n-veille-rss/`. Tu verras qu'on **skip `/design`**, que le PRD met l'accent sur les **credentials externes** et l'**idempotence**, et qu'un squelette de workflow JSON est fourni à titre pédagogique.
- **Tu veux faire une app desktop native** (macOS, Windows ou Linux) → `desktop-jarvis-macos/`. Tiré d'un vrai projet : une codebase par OS (ici macOS seul), logique métier isolée dans une bibliothèque testable, `ui_tests:` expliqué, plan de phase avec critères « Fait quand » et lignes `Sécurité :`, puis un SPEC d'évolution (`/evoluer`).
- **Tu veux faire une app mobile native** (iOS et/ou Android) → `mobile-suivi-habitudes/`. Tu verras la règle « une plateforme à la fois » (iOS pilote, portage Android en Phase 3), les contraintes de build et de comptes développeur dans les risques, et un plan dont chaque tâche UI nomme son flow Maestro.
- **Tu as déjà un projet qui n'a jamais eu de PRD** et tu veux y ajouter une feature → `feature-projet-existant/`. Tu verras ce que `/feature` produit : le brief avec sa section Sécurité, un PRD reconstitué (V1 existant + V2 en cours), STATUS et STRUCTURE minimaux, une `security-checklist.md` d'exemple, et un exemple de ligne `Sécurité :` et de verdict du gate (voir son README).
- **Tu as déjà une app mobile ou desktop native sans PRD** → `feature-mobile-existant/` (Android, permission de notification, composants du manifeste) ou `feature-desktop-existant/` (Tauri, système de fichiers, portée des permissions). Tu verras ce que `/feature` lit en plus sur un projet natif (`references/native-discovery.md`), la ligne `ui_tests:` du `CLAUDE.md` (par OS pour le desktop), et une `security-checklist.md` adaptée à la plateforme.

## Format commun à tous les exemples

Chaque dossier contient au minimum :

| Fichier | Produit par | À quoi ça sert |
|---------|-------------|---------------|
| `CLAUDE.md` | `/start` + `/architect` + manuel | Identité + Stack + Request Classification + Conventions/Instructions/Contexte métier |
| `PRD.md` | `/architect` | Sommaire + Utilisateurs + MVP + Hors-MVP + Phases + Stack + Critères de succès |
| `phase-N-plan.md` | `/plan` | *(webapp uniquement)* Plan détaillé d'une phase avec critères "Fait quand" |
| `DESIGN.md` | `/design` | *(webapp uniquement)* Design system Google `design.md` (YAML tokens + 8 sections) |
| `*.workflow.json` | manuel via n8n UI | *(automation uniquement)* Squelette du workflow n8n |
| `docs/plans/phase-N-plan.md` | `/plan` | *(desktop et mobile aussi)* Tâches avec « Fait quand » nommant le test natif ou le flow Maestro |
| `docs/specs/SPEC-*.md` | `/evoluer` | *(desktop uniquement)* Spécification d'une évolution V2 |
| `docs/features/*.md` | `/feature` | *(feature-projet-existant uniquement)* Brief de cadrage sur projet existant |
| `docs/security-checklist.md` | manuel, suggéré par `/feature` | *(feature-projet-existant uniquement)* Règles sécurité propres au projet, lues par `tool-security-gate` |

Ouvre ces fichiers en parallèle de tes propres essais — le format est plus parlant qu'une longue doc.

> Note : ces projets sont illustratifs. Tu n'as **pas** à reproduire ces projets ni à utiliser la même stack. Le kit s'adapte à ton cas via `project_type` et les providers favoris que tu déclares dans `/architect`.
