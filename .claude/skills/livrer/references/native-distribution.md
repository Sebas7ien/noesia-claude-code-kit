# Distribution native — desktop & mobile (Étape 3)

> **Lis ce fichier uniquement si** `project_type` ∈ `{desktop, mobile}`. Le flow hosting web (Vercel/Netlify/…) et le domaine custom (Étape 3.5) ne s'appliquent pas.
>
> Règle du skill inchangée : chaque commande est **affichée puis validée** (`oui / modifie / skip`) avant exécution. Rien n'est soumis à un store sans accord explicite. Les clés de signature, mots de passe et profils restent hors du repo et hors de la conversation.

## 0 — Canal de distribution (question d'entrée)

Lis `## Stack` / le PRD (choix de l'Étape 2b d'`/architect`). Si absent, demande par plateforme :

- **Test interne** (TestFlight, Google Play internal testing, build signé partagé à la main)
- **Store public** (App Store, Google Play, Mac App Store, Microsoft Store)
- **Distribution directe** (DMG notarisé, installeur/MSIX Windows, Flatpak/AppImage/.deb Linux)

Écris le choix dans `## Stack` (`distribution: …`). Une plateforme à la fois, la plateforme pilote d'abord.

## 1 — Prérequis communs (à confirmer avant de builder)

- [ ] Numéro de version et numéro de build incrémentés (`CFBundleShortVersionString`/`CFBundleVersion`, `versionName`/`versionCode`, version du manifeste MSIX, `Cargo.toml`)
- [ ] Build **Release** local sans erreur, tests verts **et suite de tests UI (`ui_tests:`, mobile ou desktop) passée sur le build Release** (cf. `/execute` Étape 2.5)
- [ ] Aucun secret en clair dans le code (`grep -rE 'sk-|Bearer |eyJ' ios android macos windows linux`)
- [ ] Fichiers de signature gitignorés (`git check-ignore` sur `*.p12 *.jks *.keystore *.pfx *.mobileprovision`)
- [ ] Machine de build adaptée à la cible (macOS pour iOS/macOS, Windows pour WinUI) — sinon runner CI

## 2 — Mobile

### iOS
1. Compte Apple Developer actif ; `Signing & Capabilities` configuré dans Xcode (signature automatique recommandée au début).
2. Archive : `xcodebuild -scheme {scheme} -configuration Release -archivePath build/{App}.xcarchive archive`
3. Export + upload : `xcodebuild -exportArchive …` puis upload via Xcode Organizer ou `xcrun altool`/`xcrun notarytool` selon le cas (préférer l'Organizer, le plus simple à valider à l'œil).
4. **TestFlight d'abord** (testeurs internes), puis soumission App Store Connect : fiche de l'app, captures, **politique de confidentialité (URL)**, questionnaire de confidentialité des données, classification d'âge.

### Android
1. Keystore d'upload créée **hors du repo** (`keytool -genkeypair …`), référencée via `keystore.properties` gitignoré ; activer Play App Signing.
2. Bundle signé : `cd android && ./gradlew bundleRelease` (→ `.aab`).
3. Upload dans Google Play Console → **Internal testing** d'abord, puis closed/open, puis production. Prévoir : fiche store, captures, politique de confidentialité, formulaire *Data safety*, content rating.

## 3 — Desktop

### macOS
- **Direct** : archive + export Developer ID, puis **notarisation** : `xcrun notarytool submit {App}.zip --keychain-profile "{profil}" --wait` puis `xcrun stapler staple {App}.app`, enfin DMG (`hdiutil create …`). Vérifier : `spctl --assess --type execute -vv {App}.app`.
- **Mac App Store** : même flux que iOS (archive → App Store Connect).

### Windows
- Packaging **MSIX** depuis Visual Studio (*Package and Publish*) ou `dotnet publish -c Release`. Signature avec un certificat de code (`signtool sign …`) ; hors signature, SmartScreen avertira l'utilisateur — le dire.
- **Microsoft Store** : soumission via Partner Center (signature gérée par le Store).
- Build sur une machine Windows ou un runner CI `windows-latest`.

### Linux
- **Flatpak** (recommandé, Flathub) : manifeste `{app-id}.yml`, `flatpak-builder --user --install build {app-id}.yml`. Alternatives : AppImage, `.deb` (`cargo deb`, dépendance à signaler).

## 4 — Smoke test (remplace `browser-verifier`)

Pas d'URL à visiter : installe l'artefact **tel que l'utilisateur le recevra** et lance-le.

- iOS : installation via TestFlight (action de l'utilisateur) ou `.ipa` de test sur appareil/simulateur ; Android : `adb install` du `.apk` extrait du `.aab` (`bundletool`) ou installation depuis la piste interne.
- Desktop : installer le DMG/MSIX/Flatpak sur une machine propre (ou VM), lancer, ouvrir le parcours principal.
- Critères : l'app démarre sans crash, l'écran principal s'affiche, une action clé du PRD fonctionne, aucune erreur dans les logs (`Console.app`/`log stream`, `adb logcat`, Event Viewer, `journalctl`).
- Demande à l'utilisateur de confirmer le résultat observé ; une capture d'écran est un plus.

## Retour au SKILL.md

Une fois l'artefact distribué (ou soumis) et le smoke test fait, passe à l'Étape 5 en renseignant `Distribution`, `Version/build` et `Statut` (en review / publié / test interne) à la place de l'URL de prod.
