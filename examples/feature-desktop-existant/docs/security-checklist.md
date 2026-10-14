# Checklist sécurité — carnet-notes

> Règles propres au projet, lues par `tool-security-gate` en plus de la baseline du kit.
> Une règle par ligne, avec un indice de grep quand c'est possible.
> Ce fichier est créé **après** `/feature`, quand le brief a signalé « baseline seule ».

## Pont interface ↔ Rust
- L'interface n'a aucune portée fs directe : pas de `fs:` large dans les capabilities — `grep -rn "fs:" src-tauri/capabilities/`
- Chaque commande exposée est listée et nécessaire — `grep -n "generate_handler" src-tauri/src/`
- Le contenu de la fenêtre est local : pas de page distante chargée, CSP définie — `grep -n "csp\|devUrl\|url" src-tauri/tauri.conf.json`

## Système de fichiers
- Le dossier cible vient de la boîte de dialogue système, jamais d'une chaîne libre envoyée par l'interface — `grep -rn "target_dir" src-tauri/src/`
- Les noms de fichiers exportés sont dérivés d'un identifiant, pas du titre — `grep -rn "File::create\|fs::write" src-tauri/src/`
- Un export ne supprime ni n'écrase aucun fichier existant — `grep -rn "remove_file\|remove_dir\|OpenOptions" src-tauri/src/`
- Pas de `unwrap()` dans une commande (un chemin invalide ne doit pas faire planter l'application) — `grep -rn "unwrap()" src-tauri/src/commands.rs`

## Données
- Aucune note dans les logs — `grep -rn "println!\|log::" src-tauri/src/`
- Aucun accès réseau sans décision explicite — `grep -rn "reqwest\|http" src-tauri/Cargo.toml`

## Release
- Aucune clé de signature ni certificat dans le repo — `git ls-files | grep -E "\.(pfx|p12|key|pem)$"` ne retourne rien
