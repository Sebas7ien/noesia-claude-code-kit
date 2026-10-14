# Checklist sécurité — factures-api

> Règles propres au projet, lues par `tool-security-gate` en plus de la baseline du kit.
> Une règle par ligne, avec un indice de grep quand c'est possible.
> Exemple : ce fichier est ce que tu crées **après** `/feature`, quand le brief a signalé « baseline seule ».

## Données clients
- Aucune adresse e-mail ni montant de facture dans les logs — `grep -rn "logger\.\(info\|debug\)" src/ | grep -i "email\|amount"`
- Toute requête sur `invoices` filtre par `cabinet_id` du compte connecté — `grep -rn "FROM invoices" src/`

## E-mail sortant
- Clé du service d'e-mail lue depuis `process.env`, jamais en dur — `grep -rn "re_[A-Za-z0-9]\{10,\}" src/`
- Destinataire pris dans la table `clients`, jamais dans le corps d'une requête HTTP — `grep -rn "to:" src/mail/`
- Le contenu du message n'inclut que le numéro de facture, le montant et l'échéance

## Jobs planifiés
- Un job qui envoie à des tiers marque `reminded_at` dans la même transaction que la sélection (pas de double envoi)
- Aucun endpoint HTTP ne déclenche le job sans authentification — `grep -rn "sendReminders" src/routes/`

## SQL
- Requêtes paramétrées (`$1`, `$2`), jamais de template string — `grep -rnE "query\(\`" src/`
