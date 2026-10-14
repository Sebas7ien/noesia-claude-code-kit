# GitHub Pages — procédure deploy (hosting = github-pages)

> **Lis ce fichier uniquement si** `/livrer` Étape 1.2 détecte `hosting: GitHub Pages`.
>
> Typique pour site statique Next.js avec `output: 'export'`.

## Pré-requis (à vérifier avant le premier deploy)

- Le site est **100 % statique** : `output: 'export'` dans `next.config.*` (build dans `out/`), aucune Server Action, route API, middleware ni SSR. Sinon GitHub Pages est impossible → retour à l'Étape 1.3 du SKILL.md pour choisir un autre hosting.
- **Chemin de base** : un site « projet » est servi sous `https://{user}.github.io/{repo}/` → `basePath: '/{repo}'` (et `images: { unoptimized: true }`) dans `next.config.*`, sauf si domaine custom ou repo nommé `{user}.github.io`.
- GitHub → Settings → Pages → **Source : GitHub Actions**.
- Workflow `.github/workflows/deploy.yml` (s'il n'existe pas, le proposer, jamais l'écrire sans validation) : `actions/checkout` → install + `npm run build` → `actions/configure-pages` → `actions/upload-pages-artifact` (path `out`) → `actions/deploy-pages`, avec permissions `pages: write` et `id-token: write`, déclenché sur `push` vers `main`. Épingler les versions des actions.
- Pas de secret côté serveur : tout ce qui est dans le bundle est public.

## Commandes de base

```bash
npm run build
git push origin main     # workflow .github/workflows/deploy.yml gère le push gh-pages
```

Pour chaque commande : affiche, demande *"J'exécute ? (oui / modifie / skip)"*, attends réponse.

## Domaine custom côté GitHub Pages

Quand Étape 3.5 du SKILL.md demande d'ajouter un domaine custom et que l'hosting est GitHub Pages :

Affiche : *"Va dans Settings → Pages → Custom domain → ajoute `{URL_CIBLE}` et crée un fichier `CNAME` à la racine du repo contenant l'URL. Note la valeur DNS demandée et colle-la moi."* Puis continue avec Étape 3.5.4 (DNS registrar-aware).
