---
name: tool-desktop-visual-check
description: Vérification visuelle obligatoire de l'app desktop native lancée (macOS, Windows, Linux) pour toute tâche UI (fenêtre, menu, barre d'outils, boîte de dialogue, thème clair/sombre, mise à l'échelle HiDPI, redimensionnement). Équivalent desktop de tool-playwright-visual-check. Skill générique (config globale) : s'adapte à l'outillage réel de chaque repo et à l'OS de la machine plutôt que de supposer schemes, chemins ou scripts précis.
---

# Vérification visuelle UI desktop (app native lancée)

Ce skill vit dans la config globale (`claude-config/skills/`) : il doit marcher
sur n'importe quel repo desktop natif. Il ne connaît pas à l'avance le scheme,
l'exécutable ni les scripts d'un projet — chaque étape dit quoi *chercher*.
Playwright (navigateur) ne s'applique jamais à une app native : utiliser ce skill.

## Quand utiliser ce skill
Dès qu'une tâche modifie l'apparence d'une app desktop : fenêtre, layout,
menus, barre d'outils, dialogues, thème clair/sombre, échelle d'affichage,
redimensionnement, icônes, animations.

## Règle
Ne jamais considérer une tâche UI terminée sur la seule lecture du code ou la
compilation. Lancer l'app **dans cette session** et regarder le rendu réel.
**Un OS ne se vérifie que sur cet OS.** Si l'OS cible n'est pas celui de la
machine (ex : WinUI depuis macOS), ne pas prétendre avoir vérifié : le dire,
et passer par un runner CI ou une VM, ou demander à l'utilisateur de fournir
les captures.

## Auto-correction
Si une étape ne correspond pas au repo (commande obsolète, outil absent) :
s'adapter au réel (README, scheme Xcode, `.csproj`, `Cargo.toml`, `ui_tests:`
dans `CLAUDE.md ## Stack`), signaler l'écart, et si l'erreur vient du skill
lui-même, proposer de le corriger ensuite.

## Entrées attendues
- **Les fenêtres/écrans/menus concernés** par le changement.
- **L'OS cible** et l'OS de la machine (`uname -s` / `sw_vers` / `ver`).
- **Un état de données** si l'écran en dépend (compte, fichier d'exemple, base locale de test).

## 1. Préparer l'environnement

Chercher d'abord l'outillage du repo (script, `Makefile`, tâche IDE) avant
d'improviser. Noter si l'app **tournait déjà** avant (nettoyage, §5).

- **macOS** : `xcodebuild -scheme {scheme} -configuration Debug build` puis `open {chemin}/{App}.app`
- **Windows** : `dotnet build` puis lancer l'exécutable (`Start-Process …`)
- **Linux** : `cargo run` (session graphique, ou `xvfb-run` pour un rendu headless)

Si l'outil de test UI de l'OS (`ui_tests:` : XCUITest, FlaUI, dogtail,
Appium) permet d'atteindre l'écran, l'utiliser pour naviguer de façon
reproductible et pour capturer la fenêtre (`XCUIApplication().windows.firstMatch.screenshot()`, `window.Capture()` de FlaUI, capture dogtail).

## 2. État de données

Repérer comment le repo fournit un état de test (fichier d'exemple, base
SQLite de dev, argument de lancement). Si rien n'est disponible, le demander
à l'utilisateur plutôt que de vérifier un écran vide.

## 3. Capturer

Capturer puis **lire chaque image** (Read) pour la décrire. Sortie dans le
scratchpad ou `tmp/` (jamais committé).

- **macOS** : `screencapture -x tmp/macos-{ecran}-{etat}.png` (écran entier) ; fenêtre seule : `screencapture -l{windowID}` (ID via `osascript`/`CGWindowListCopyWindowInfo`) ou capture XCUITest. Permission *Enregistrement de l'écran* requise la première fois.
- **Windows** : capture via PowerShell (`System.Drawing` + `CopyFromScreen`) ou `window.Capture()` de FlaUI.
- **Linux** : `import -window root tmp/linux-….png` (ImageMagick) ou `gnome-screenshot -f` / `grim` (Wayland) ; sous Xvfb : `import -display :99 -window root`.

États à couvrir selon le changement :
- **Thème clair ET sombre** — macOS : `osascript -e 'tell application "System Events" to tell appearance preferences to set dark mode to true'` ; Windows : clé `AppsUseLightTheme` sous `HKCU\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize` ; Linux : `gsettings set org.gnome.desktop.interface color-scheme 'prefer-dark'` (ou `GTK_THEME=Adwaita:dark`)
- **Tailles de fenêtre** : minimale, par défaut, grande/plein écran — le contenu doit se réorganiser sans chevauchement ni troncature
- **Échelle d'affichage / HiDPI** — Windows 100/150/200 %, Linux `GDK_SCALE=2`, macOS Retina vs non-Retina si pertinent
- **États d'interface** : menu ouvert, dialogue modal, focus clavier visible, élément désactivé, liste longue, état vide/erreur
- **Avant/après** pour un correctif visuel
- **Animations** : attendre la fin avant de capturer l'état stable
- **Langue** (textes plus longs) seulement si le changement touche au texte

Relever aussi les logs pendant l'usage — macOS : `log stream --predicate 'process == "{App}"'` ; Windows : sortie console / Event Viewer ; Linux : stderr / `journalctl --user`.

## 4. Comparer et corriger

Comparer chaque capture à l'attendu (HIG macOS, Fluent Windows, GNOME HIG
Linux, `DESIGN.md`/direction de `design-ui-proposal` s'ils existent) : contraste
en sombre, éléments coupés au redimensionnement, menus et raccourcis non
conformes à la plateforme, focus clavier invisible, flou en HiDPI. Corriger
le code et recapturer.

## 5. Nettoyer

Restaurer les réglages modifiés (thème clair, échelle d'affichage, taille de
fenêtre sauvegardée dans les préférences). Fermer l'app **uniquement** si ce
skill l'a lancée ; ne pas toucher à une instance que l'utilisateur utilisait.

## 6. Auto-évaluation avant de conclure

Relire la liste ci-dessous et vérifier qu'aucun cas ne s'applique. Sinon,
corriger avant de conclure.

## Ce qu'il ne faut jamais faire
- Annoncer « ça compile / ça devrait s'afficher » sans capture réelle regardée
- Prétendre avoir vérifié un OS qui n'est pas celui de la machine
- Utiliser Playwright / un navigateur pour une app native
- Vérifier une seule taille de fenêtre ou un seul thème quand le changement est concerné
- Laisser le thème système ou l'échelle d'affichage modifiés, ou fermer une instance utilisée par l'utilisateur
- Appliquer aveuglément un détail obsolète du skill au lieu de s'adapter et de signaler l'écart

**Prochaine étape** : retour à /execute (ou /validate) avec les captures.
