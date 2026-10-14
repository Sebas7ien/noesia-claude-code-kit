# Outils de test UI desktop — vérification + install (Étape 5a, `project_type: desktop`)

> **Lis ce fichier uniquement si** `project_type: desktop`. Équivalent desktop de `playwright-install.md`. L'outil se choisit **par OS** dans `/architect` (ligne `ui_tests:` de `## Stack`, ex : `macos: XCUITest, windows: FlaUI, linux: dogtail`).
>
> **Ne rien installer sans accord** (dépendance à signaler : nom, raison, alternative). Chaque outil ne tourne que sur son OS : si l'OS cible n'est pas celui de la machine, le noter et prévoir un runner CI.

## Par OS

| OS | Outil | Vérifier | Installer si absent (à valider) |
|----|-------|----------|---------------------------------|
| macOS | **XCUITest** (natif) | Target *UI Testing Bundle* (macOS) dans le projet ; `xcodebuild test -scheme {scheme} -destination 'platform=macOS'` | Xcode : *File > New > Target > UI Testing Bundle* (aucune dépendance). Au 1er lancement, macOS demande la permission *Accessibilité* |
| macOS | **Appium + Mac2 driver** | `appium driver list --installed` montre `mac2` | `npm i -g appium` puis `appium driver install mac2` |
| Windows | **FlaUI** (UI Automation, .NET) | Projet de test avec les paquets NuGet `FlaUI.Core` + `FlaUI.UIA3` ; `dotnet test` | `dotnet add package FlaUI.UIA3` dans le projet de tests (signaler) |
| Windows | **Appium + Windows driver** (s'appuie sur WinAppDriver) | `appium driver list --installed` montre `windows` ; mode développeur Windows activé | `npm i -g appium` puis `appium driver install --source=npm appium-windows-driver` (état de maintenance de WinAppDriver à vérifier avant adoption) |
| Linux | **dogtail** (accessibilité AT-SPI, Python) | `python3 -c "import dogtail"` ; session avec AT-SPI actif | `pip install dogtail` dans un venv de test (signaler) |
| Linux | **`cargo test` + Xvfb** (tests de widgets GTK 4 headless) | `xvfb-run cargo test` | `apt install xvfb` |
| Tous | **WebdriverIO** (via Appium) | `npx wdio --version` + drivers Appium ci-dessus | `npm init wdio@latest e2e` (ajoute Node/npm et de nombreux paquets : signaler) |

## Test rapide (optionnel)

*"Tu veux que je lance un test de fumée (ouvrir l'app, vérifier que la fenêtre principale s'affiche, prendre une capture) ? (oui/skip)"*

## Retour au SKILL.md

Une fois l'outil vérifié pour chaque OS retenu (présent, installé ou noté « runner CI »), reviens à l'Étape 5b du SKILL.md.
