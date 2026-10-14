# Outils de test UI mobile — vérification + install (Étape 5a, `project_type: mobile`)

> **Lis ce fichier uniquement si** `project_type: mobile`. Équivalent mobile de `playwright-install.md` : sans outil de test UI, `/execute` Étape 2.5 ne peut pas vérifier le parcours utilisateur sur simulateur/émulateur.
>
> Le choix de l'outil est fait dans `/architect` (ligne `ui_tests:` de `## Stack`). Ici : vérifier que l'outil retenu est présent. **Ne rien installer sans accord** (dépendance à signaler : nom, raison, alternative).

## Prérequis communs

- iOS : `xcodebuild -version`, `xcrun simctl list devices available` (au moins un simulateur)
- Android : `adb version`, `echo $ANDROID_HOME`, `emulator -list-avds` (au moins un AVD)

## Par outil

| Outil | Plateformes | Vérifier | Installer si absent (à valider) |
|-------|-------------|----------|---------------------------------|
| **XCUITest** | iOS | Un target *UI Testing Bundle* existe dans le projet Xcode ; `xcodebuild test -scheme {scheme} -destination 'platform=iOS Simulator,name={device}'` | Xcode : *File > New > Target > UI Testing Bundle* (aucune dépendance) |
| **Espresso** (+ Compose UI test) | Android | Dossier `android/app/src/androidTest/` ; dépendances `androidx.test.espresso:espresso-core` et `androidx.compose.ui:ui-test-junit4` dans `build.gradle.kts` ; `./gradlew connectedAndroidTest` | Ajouter ces dépendances `androidTestImplementation` (signaler) |
| **Maestro** | iOS (simulateur) + Android | `maestro --version` ; Java 17+ (`java -version`) | `brew tap mobile-dev-inc/tap && brew install maestro` (ou script officiel `curl -Ls "https://get.maestro.mobile.dev" \| bash`) |
| **Appium** | iOS + Android | `appium --version` ; `appium driver list --installed` doit montrer `xcuitest` et/ou `uiautomator2` | `npm i -g appium` puis `appium driver install xcuitest` / `appium driver install uiautomator2` |
| **WebdriverIO** (+ Appium) | iOS + Android | Appium ci-dessus + `wdio.conf.ts` dans `e2e/` ; `npx wdio --version` | `npm init wdio@latest e2e` (choisir *Mobile testing* + service Appium) — ajoute Node/npm et ~dizaines de paquets : à signaler |

## Test rapide (optionnel)

*"Tu veux que je lance un test de fumée (ouvrir l'app sur le simulateur/émulateur et prendre une capture) ? (oui/skip)"* — selon l'outil : `maestro test` sur un flow `launchApp` + `takeScreenshot`, ou la commande `xcodebuild test` / `./gradlew connectedAndroidTest`.

## Retour au SKILL.md

Une fois l'outil vérifié (présent ou installé), reviens à l'Étape 5b du SKILL.md.
