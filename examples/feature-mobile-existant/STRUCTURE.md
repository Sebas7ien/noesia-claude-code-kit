# STRUCTURE.md — Carte d'architecture du projet

> Créé par `/feature` à partir de l'arborescence existante.

## Arborescence

<!-- architect:directories -->
- `app/src/main/java/.../ui/` — écrans Compose et ViewModels
- `app/src/main/java/.../data/` — entités Room, DAO, base
- `app/src/main/AndroidManifest.xml` — permissions et composants déclarés
- `app/src/androidTest/` — tests Compose UI
- `.maestro/` — flows E2E (`ajout-article.yaml`, `cocher-article.yaml`)
<!-- /architect:directories -->

## Intégrations externes

<!-- structure:integrations -->
- Aucune (pas de réseau, pas de backend)
<!-- /structure:integrations -->

## Fichiers clés

<!-- structure:key-files -->
- `app/build.gradle.kts` — `minSdk`, `targetSdk`, dépendances
- `app/src/main/AndroidManifest.xml` — seule permission actuelle : aucune
- `app/src/main/java/.../data/ListeDao.kt` — accès aux listes et articles
<!-- /structure:key-files -->

## Évolutions livrées (résumé)

<!-- structure:evolutions-summary -->
_(Maintenu par /evoluer après chaque livraison.)_
<!-- /structure:evolutions-summary -->
