---
name: tool-mobile-visual-check
description: Vérification visuelle obligatoire sur simulateur iOS / émulateur Android pour toute tâche UI d'une app mobile native (écran SwiftUI/Compose, navigation, thème clair/sombre, taille de police, orientation, composant). Équivalent mobile de tool-playwright-visual-check. Skill générique (config globale) : s'adapte à l'outillage réel de chaque repo plutôt que de supposer des schémas, appareils ou scripts précis.
---

# Vérification visuelle UI mobile (simulateur / émulateur)

Ce skill vit dans la config globale (`claude-config/skills/`) : il doit marcher
sur n'importe quel repo mobile. Il ne connaît pas à l'avance le scheme Xcode,
l'applicationId, les appareils ni les scripts d'un projet — chaque étape dit
quoi *chercher* dans le repo courant plutôt que de supposer une valeur fixe.
Playwright (navigateur) ne s'applique jamais à une app native : utiliser ce skill.

## Quand utiliser ce skill
Dès qu'une tâche modifie l'apparence d'une app mobile : écran, composant,
layout, navigation, thème clair/sombre, typographie, animation, adaptation
tablette/orientation.

## Règle
Ne jamais considérer une tâche UI terminée sur la seule lecture du code ou la
compilation. Toujours lancer l'app sur simulateur/émulateur, **dans cette
session**, et regarder le rendu réel avant de conclure — ne jamais renvoyer la
vérification à l'utilisateur.

## Auto-correction
Si une étape ne correspond pas au repo (scheme renommé, commande obsolète,
outil absent) : s'adapter au réel (README, `*.xcodeproj`, `build.gradle.kts`,
`.maestro/`, `ui_tests:` dans `CLAUDE.md ## Stack`), signaler l'écart, et si
l'erreur vient du skill lui-même, proposer de corriger ce fichier ensuite.

## Entrées attendues
- **Les écrans/parcours concernés** par le changement (ne pas deviner : un
  composant partagé impacte plusieurs écrans).
- **La/les plateforme(s)** : iOS, Android, ou les deux (si le PRD dit parité,
  vérifier les deux).
- **Un compte de test** si l'écran est derrière une connexion.

## 1. Préparer l'environnement

Chercher d'abord l'outillage du repo (script, `Makefile`, skill projet, flows
`.maestro/`, schemes) avant d'improviser. Noter si le simulateur/émulateur
**tournait déjà** avant (détermine le nettoyage, §5).

- **iOS** : `xcrun simctl list devices booted` ; sinon `xcrun simctl boot "{device}"`.
  Build + install + lancement : `xcodebuild -scheme {scheme} -destination 'platform=iOS Simulator,name={device}' build`, puis `xcrun simctl install booted {App}.app` et `xcrun simctl launch booted {bundle-id}`.
- **Android** : `adb devices` ; sinon `emulator -avd {avd} &` puis `adb wait-for-device`.
  Build + install + lancement : `cd android && ./gradlew installDebug`, puis `adb shell am start -n {applicationId}/.MainActivity`.

Si l'outil de test UI du projet (`ui_tests:`) permet d'atteindre l'écran
(flow Maestro, test XCUITest/Espresso, script Appium), l'utiliser pour naviguer
de façon reproductible plutôt que de taper à la main.

## 2. Login / état de données

Repérer comment le repo fournit un état de test (compte dans un fichier local
gitignoré, backend de dev, données seed, argument de lancement). Si aucun
identifiant n'est disponible : **demander directement à l'utilisateur** le
compte de test adapté et faire la vérification soi-même.

## 3. Naviguer et capturer

Capturer puis **lire chaque image** (Read) pour la décrire — une capture non
regardée ne vaut pas vérification. Dossier de sortie : le scratchpad ou
`tmp/` (jamais committé).

- **iOS** : `xcrun simctl io booted screenshot tmp/ios-{ecran}-{etat}.png`
- **Android** : `adb exec-out screencap -p > tmp/android-{ecran}-{etat}.png`
- **Maestro** (si présent) : commande `takeScreenshot` dans le flow ; `maestro hierarchy` pour inspecter l'arbre d'éléments.

États à couvrir selon le changement :
- **Thème clair ET sombre** — iOS : `xcrun simctl ui booted appearance light|dark` ; Android : `adb shell cmd uimode night yes|no`
- **Taille de texte agrandie** (accessibilité) — iOS : `xcrun simctl ui booted content_size extra-large` ; Android : `adb shell settings put system font_scale 1.3`
- **Petit ET grand écran** — ex : iPhone SE / Pro Max, petit téléphone / grand téléphone, tablette si supportée ; Android : `adb shell wm size 720x1280` (puis `wm size reset`)
- **Orientation** si supportée — Android : `adb shell settings put system accelerometer_rotation 0` + `user_rotation 1` ; iOS : via un test XCUITest (`XCUIDevice.shared.orientation`)
- **États d'écran** : vide, chargement, erreur, liste longue, clavier ouvert, zones sûres (encoche / barre de navigation)
- **Avant/après** pour un correctif visuel
- **Animations** : attendre la fin avant de capturer l'état stable
- **Langue** (texte plus long, RTL) seulement si le changement touche au texte

Relever aussi les logs pendant la navigation — une régression visuelle
s'accompagne souvent d'une erreur : `xcrun simctl spawn booted log stream --predicate 'process == "{App}"'` (iOS), `adb logcat -d | tail -200` (Android).

## 4. Comparer et corriger

Comparer chaque capture à l'attendu (HIG pour iOS, Material 3 pour Android,
`DESIGN.md`/direction de `design-ui-proposal` s'ils existent) : texte tronqué,
contraste insuffisant en sombre, contenu sous l'encoche ou la barre système,
cible tactile trop petite (< 44 pt iOS / 48 dp Android), débordement en police
agrandie. Corriger le code et recapturer — ne pas rapporter un bug sans le
corriger si c'est dans la portée de la tâche.

## 5. Nettoyer

**Restaurer les réglages modifiés** pour la vérification (thème, taille de
police, taille d'écran, rotation) : `simctl ui … appearance light`,
`content_size large`, `adb shell cmd uimode night no`, `font_scale 1.0`,
`wm size reset`. Éteindre le simulateur/émulateur (`xcrun simctl shutdown`,
`adb emu kill`) **uniquement** si c'est ce skill qui l'a démarré ; sinon le laisser tourner.

## 6. Auto-évaluation avant de conclure

Relire la liste ci-dessous et vérifier qu'aucun cas ne s'applique. Sinon,
corriger avant de conclure.

## Ce qu'il ne faut jamais faire
- Annoncer « ça compile, c'est bon » ou « ça devrait s'afficher » sans capture réelle regardée
- Utiliser Playwright / un navigateur pour une app native
- Vérifier une seule plateforme quand le PRD exige la parité iOS/Android
- Vérifier un seul thème, une seule taille de police ou un seul gabarit d'écran quand le changement est concerné
- Renvoyer la vérification à l'utilisateur faute de compte de test — le demander
- Laisser le simulateur dans un état modifié (sombre, police 1.3, `wm size`) ou couper un émulateur que l'utilisateur utilisait
- Appliquer aveuglément un détail obsolète du skill au lieu de s'adapter et de signaler l'écart

**Prochaine étape** : retour à /execute (ou /validate) avec les captures.
