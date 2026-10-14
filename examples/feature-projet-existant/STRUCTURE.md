# STRUCTURE.md — Carte d'architecture du projet

> Créé par `/feature` à partir de l'arborescence existante.

## Arborescence

<!-- architect:directories -->
- `src/clients/` — routes, service et dépôt des clients
- `src/invoices/` — routes, service et dépôt des factures
- `src/db/` — connexion `pg` et migrations SQL
- `src/middleware/` — gestion d'erreurs (`AppError` → réponse HTTP)
<!-- /architect:directories -->

## Intégrations externes

<!-- structure:integrations -->
- PostgreSQL — seule intégration actuelle (aucun service tiers)
<!-- /structure:integrations -->

## Fichiers clés

<!-- structure:key-files -->
- `src/invoices/invoice-service.ts` — logique métier des factures
- `src/middleware/error-handler.ts` — conversion `AppError` → HTTP
<!-- /structure:key-files -->

## Évolutions livrées (résumé)

<!-- structure:evolutions-summary -->
_(Maintenu par /evoluer après chaque livraison.)_
<!-- /structure:evolutions-summary -->
