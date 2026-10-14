# Checklist sécurité — liste-courses

> Règles propres au projet, lues par `tool-security-gate` en plus de la baseline du kit.
> Une règle par ligne, avec un indice de grep quand c'est possible.
> Ce fichier est créé **après** `/feature`, quand le brief a signalé « baseline seule ».

## Permissions
- Aucune permission n'est demandée au lancement ; `POST_NOTIFICATIONS` est demandée à l'activation du rappel — `grep -rn "requestPermission\|POST_NOTIFICATIONS" app/src/main`
- Pas de permission d'alarme exacte ni de localisation sans décision explicite — `grep -n "SCHEDULE_EXACT_ALARM\|USE_EXACT_ALARM\|ACCESS_.*LOCATION" app/src/main/AndroidManifest.xml`

## Composants Android
- Tout composant du manifeste a `android:exported` explicite, `false` sauf nécessité — `grep -n "<receiver\|<service\|<activity" app/src/main/AndroidManifest.xml`
- Les `PendingIntent` utilisent `FLAG_IMMUTABLE` — `grep -rn "PendingIntent\." app/src/main`

## Données
- La notification affiche un nombre d'articles, jamais leur contenu, et `setVisibility(VISIBILITY_PRIVATE)` — `grep -rn "NotificationCompat" app/src/main`
- Aucune donnée de liste dans les logs — `grep -rn "Log\.[dive](" app/src/main`
- Aucun accès réseau : pas de permission `INTERNET` sans décision explicite — `grep -n "INTERNET" app/src/main/AndroidManifest.xml`

## Release
- Le keystore et ses mots de passe sont hors du repo — `git ls-files | grep -E "\.(jks|keystore)$"` ne retourne rien
