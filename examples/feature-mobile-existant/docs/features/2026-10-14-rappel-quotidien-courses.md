# Feature : rappel quotidien par notification

## En 1 phrase
Afficher chaque jour, à l'heure choisie, une notification locale « N articles à acheter » qui ouvre la liste.

## Contexte projet (reconstitué)
- Stack : Kotlin 2 + Compose + Room, `minSdk 26` / `targetSdk 35` · type : mobile (Android) · livré : non (APK de test)
- Conventions : un écran = `@Composable` + `ViewModel`, accès base via DAO, tests Compose UI + flows Maestro
- Outil de test UI : Compose UI tests (`androidTest/`) et Maestro (`.maestro/`) : présents

## Besoin
- Pour qui / pour quoi : le foyer oublie d'ouvrir la liste avant de passer au magasin ; deux oublis la semaine dernière.
- Critère de succès : une notification s'affiche à l'heure choisie chaque jour, y compris après redémarrage du téléphone ; refuser la permission ne bloque rien d'autre.

## Intégration dans le code
- `app/src/main/AndroidManifest.xml` — permission `POST_NOTIFICATIONS` (Android 13+), récepteur de redémarrage si nécessaire
- `app/src/main/java/.../ui/ReglagesScreen.kt` — nouveau : choix de l'heure et activation du rappel
- `app/src/main/java/.../data/ListeDao.kt` — nouvelle requête : nombre d'articles non cochés
- `app/src/main/java/.../rappel/` — nouveau dossier : worker de rappel et construction de la notification
- `.maestro/rappel-reglage.yaml` — nouveau flow : activer le rappel et accepter la permission

## Contraintes & dépendances
- Dépendance nouvelle : `androidx.work:work-runtime-ktx` (WorkManager) — **à valider avant installation** ; alternative : `AlarmManager` (demande une permission d'alarme exacte et un récepteur de redémarrage, plus de surface sensible)
- Émulateur API 33+ requis pour tester la permission de notification
- Pas de distribution définie : test sur émulateur et sur un téléphone en APK de test

## Risques
- Rappel perdu après redémarrage ou économie d'énergie → mitigation : WorkManager, test sur émulateur redémarré.
- Permission refusée → mitigation : demandée uniquement à l'activation du rappel, message explicite, aucune tentative d'affichage sans permission.

## Ampleur
S

## Sécurité
- Surfaces sensibles touchées : permission OS (`POST_NOTIFICATIONS`) ; composants déclarés dans le manifeste (récepteur) ; contenu affiché sur l'écran verrouillé
- Données / secrets / services tiers impliqués : noms d'articles de la liste (données perso) ; aucun secret, aucun service tiers
- Checklist projet : absente → baseline seule (suggestion : créer `docs/security-checklist.md`, exemple fourni)

## Hypothèses à valider
- [ ] Un seul rappel par jour suffit (pas de rappel par liste)
- [ ] La notification n'affiche que le nombre d'articles, jamais leur contenu
- [ ] WorkManager est accepté comme dépendance
