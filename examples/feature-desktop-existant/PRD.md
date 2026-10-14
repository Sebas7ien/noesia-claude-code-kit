# PRD — carnet-notes (reconstitué par /feature)

> ⚠️ Reconstitué depuis le code existant le 2026-10-14. À relire : la Vision et les Personas sont des hypothèses confirmées en conversation, pas une spec d'origine.

**Niveau** : STANDARD

## 1. Vision

Application de notes Markdown de bureau, locale et rapide, sans compte ni synchronisation.

## 2. Personas

- **Utilisateur unique** — prend des notes toute la journée sur son poste. Douleur : ses notes sont enfermées dans le dossier de l'application, impossible de les sauvegarder ou de les réutiliser ailleurs sans les copier à la main.

## 3. Scope actuel (V2)

### Core
- [x] Créer, éditer, supprimer une note
- [x] Recherche instantanée (SQLite)
- [x] Thème clair / sombre
- [ ] Export des notes vers un dossier choisi (cf docs/features/2026-10-14-export-notes-dossier.md)

### Technique
- [x] Commandes Tauri nommées pour chaque action
- [x] Tests `cargo test` sur le dépôt de notes
- [ ] Capability fs restreinte pour l'export

## 4. Hors scope (différé)

- [ ] Synchronisation entre postes
- [ ] Version macOS

## 5. Constraints non-négociables

- Aucune donnée ne quitte le poste
- L'interface web n'a aucun accès direct au système de fichiers : tout passe par des commandes Rust

## 6. Success Criteria

- Exporter 1 000 notes prend moins de 5 secondes
- Un export ne modifie ni ne supprime jamais un fichier qui n'a pas été créé par l'export

## 7. Implementation Phases

**V1 (existant, reconstitué)** — notes, recherche, thème, stockage local (état avant /feature)
**V2 (en cours)** — Export des notes vers un dossier choisi (cf docs/features/2026-10-14-export-notes-dossier.md)

## 8. Risks & Mitigations

- **Risque** : écrasement de fichiers existants dans le dossier cible → **Mitigation** : sous-dossier daté créé par l'export, refus si le sous-dossier existe.
- **Risque** : traversée de chemin via un titre de note (`../`) → **Mitigation** : noms de fichiers dérivés d'un identifiant, jamais du titre brut.
- **Risque** : portée fs trop large dans la capability → **Mitigation** : accès au dossier choisi via la boîte de dialogue système uniquement.
