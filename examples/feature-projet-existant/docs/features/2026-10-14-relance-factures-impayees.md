# Feature : relance e-mail des factures impayées

## En 1 phrase
Envoyer automatiquement un e-mail de relance au client quand une facture reste impayée 7 jours après son échéance.

## Contexte projet (reconstitué)
- Stack : Node 22 + TypeScript + Express 5 + PostgreSQL (`pg`) · type : webapp · livré : non (pas de déploiement visible)
- Conventions : kebab-case, erreurs `AppError` + middleware, Zod aux frontières, tests Vitest co-located

## Besoin
- Pour qui / pour quoi : le cabinet (3 personnes) relance aujourd'hui à la main ; deux factures ont été oubliées le mois dernier.
- Critère de succès : une facture impayée à J+7 de l'échéance déclenche exactement un e-mail de relance au client, une seule fois, et l'envoi est tracé.

## Intégration dans le code
- `src/invoices/invoice-repository.ts` — nouvelle requête : factures impayées échues depuis ≥ 7 jours et jamais relancées
- `src/invoices/invoice-service.ts` — nouvelle méthode `sendReminders()`
- `src/jobs/` — nouveau job planifié quotidien (le dossier n'existe pas encore)
- `src/mail/` — nouveau client d'envoi (aucun envoi d'e-mail dans le code actuel)
- migration SQL : colonne `reminded_at timestamptz` sur `invoices`

## Contraintes & dépendances
- Dépendance nouvelle : un client d'envoi d'e-mail (Resend ou nodemailer + SMTP) — **à valider avant installation**
- Pas de déploiement pour l'instant : le job ne peut être testé qu'en local avec un faux serveur SMTP

## Risques
- Double envoi si le job tourne deux fois → mitigation : `reminded_at` mis à jour dans la même transaction que la sélection.
- Relance envoyée à tort (facture payée entre-temps) → mitigation : re-vérifier le statut juste avant l'envoi.

## Sécurité
- Surfaces sensibles touchées : envoi d'e-mail à des tiers (données personnelles : nom, e-mail, montant) ; secret SMTP / clé API ; requêtes SQL
- Données / secrets / services tiers impliqués : adresse e-mail et montants des clients ; clé du service d'e-mail (variable d'environnement, jamais dans le repo) ; service d'e-mail tiers
- Checklist projet : absente → baseline seule (suggestion : créer `docs/security-checklist.md`)

## Ampleur
M

## Hypothèses à valider
- [ ] 7 jours après l'échéance est bien le bon délai pour le cabinet
- [ ] Un seul rappel suffit (pas de deuxième relance)
- [ ] Le modèle de message est validé par le cabinet avant le premier envoi réel
