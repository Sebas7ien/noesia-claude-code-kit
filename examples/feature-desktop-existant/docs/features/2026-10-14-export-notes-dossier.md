# Feature : export des notes vers un dossier choisi

## En 1 phrase
Permettre d'exporter toutes les notes en fichiers `.md` dans un dossier choisi par l'utilisateur via la boîte de dialogue système.

## Contexte projet (reconstitué)
- Stack : Tauri 2 (Rust + Svelte), stockage en fichiers `.md` + index SQLite · type : desktop (Windows, Linux) · livré : non (build local)
- Conventions : logique en Rust, l'interface n'appelle que des commandes Tauri nommées ; `AppError` ; pas de `unwrap()` dans une commande
- Outil de test UI : WebdriverIO + tauri-driver sur Windows et Linux (`e2e/wdio.conf.ts`) : présent

## Besoin
- Pour qui / pour quoi : l'utilisateur veut sauvegarder ses notes et les réutiliser hors de l'application ; aujourd'hui il copie à la main le dossier de données.
- Critère de succès : un export de 1 000 notes crée un sous-dossier daté dans le dossier choisi, contenant un `.md` par note, sans toucher aux autres fichiers du dossier.

## Intégration dans le code
- `src-tauri/src/commands.rs` — nouvelle commande `export_notes(target_dir)`
- `src-tauri/src/notes.rs` — nouvelle fonction d'export : écrit un `.md` par note
- `src-tauri/capabilities/default.json` — ajout de la permission de dialogue (choix du dossier) ; aucune portée fs large côté interface
- `src/` — bouton « Exporter » dans le menu et écran de confirmation
- `e2e/export.spec.ts` — nouveau scénario : exporter et vérifier le résultat

## Contraintes & dépendances
- Dépendance nouvelle : plugin `tauri-plugin-dialog` pour la boîte de choix de dossier — **à valider avant installation** ; alternative : champ de saisie du chemin (plus risqué : chemin libre)
- Le test E2E par tauri-driver n'existe pas sous macOS : non concerné, macOS n'est pas ciblé
- Windows : build et test sur un runner CI ou une machine Windows ; ne pas prétendre l'avoir vérifié depuis Linux

## Risques
- Écrasement de fichiers du dossier cible → mitigation : sous-dossier daté créé par l'export, refus s'il existe.
- Titres de notes avec séparateurs de chemin ou noms réservés Windows → mitigation : nom de fichier dérivé de l'identifiant de la note.

## Ampleur
M

## Sécurité
- Surfaces sensibles touchées : système de fichiers (chemin choisi par l'utilisateur) ; commande exposée à l'interface web ; capability Tauri (portée des permissions)
- Données / secrets / services tiers impliqués : contenu des notes (données perso) ; aucun secret, aucun service tiers
- Checklist projet : absente → baseline seule (suggestion : créer `docs/security-checklist.md`, exemple fourni)

## Hypothèses à valider
- [ ] Un export complet à chaque fois suffit (pas d'export incrémental)
- [ ] Le format `.md` brut convient (pas de HTML ni de PDF)
- [ ] Le plugin de dialogue est accepté comme dépendance
