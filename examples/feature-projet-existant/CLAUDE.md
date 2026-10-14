# factures-api

> Exemple de CLAUDE.md complété par `/feature` (Identité + Stack déduites du code, validées par l'utilisateur).

## Identité

<!-- start:identité -->
API REST de gestion de factures pour un cabinet de 3 personnes : création de clients, de factures, suivi des paiements. Utilisée par une interface interne. Pas encore déployée publiquement.

project_type: webapp
<!-- /start:identité -->

## Stack

<!-- architect:stack -->
- Node 22 + TypeScript (strict), Express 5
- PostgreSQL via `pg` (requêtes paramétrées, pas d'ORM)
- Validation : Zod aux frontières
- Tests : Vitest (co-located `*.test.ts`)
- Hosting : non défini (pas encore déployé)
<!-- /architect:stack -->

## Request Classification

**Niveau choisi pour ce projet** : `STANDARD`

## Conventions

- Fichiers en kebab-case, un module par dossier de `src/`
- Erreurs : classes `AppError` levées dans les services, converties en réponses HTTP par un middleware
- Commits conventionnels en anglais
