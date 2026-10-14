# STRUCTURE.md — Carte d'architecture du projet

> Créé par `/feature` à partir de l'arborescence existante.

## Arborescence

<!-- architect:directories -->
- `src/` — interface Svelte (écrans, composants)
- `src-tauri/src/` — cœur Rust : dépôt de notes, index de recherche, commandes Tauri
- `src-tauri/capabilities/` — permissions accordées à l'interface (une capability par fenêtre)
- `src-tauri/tauri.conf.json` — configuration de l'application et des bundles
- `e2e/` — tests WebdriverIO (`wdio.conf.ts`, specs)
<!-- /architect:directories -->

## Intégrations externes

<!-- structure:integrations -->
- Aucune (pas de réseau, pas de compte)
<!-- /structure:integrations -->

## Fichiers clés

<!-- structure:key-files -->
- `src-tauri/src/notes.rs` — dépôt de notes (lecture, écriture, suppression)
- `src-tauri/src/commands.rs` — commandes exposées à l'interface
- `src-tauri/capabilities/default.json` — permissions de la fenêtre principale
<!-- /structure:key-files -->

## Évolutions livrées (résumé)

<!-- structure:evolutions-summary -->
_(Maintenu par /evoluer après chaque livraison.)_
<!-- /structure:evolutions-summary -->
