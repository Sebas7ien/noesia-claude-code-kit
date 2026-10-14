---
name: design-ui-proposal
description: Propose une direction de design pour une interface — palette de couleurs, typographie, style visuel (futuriste ou non) — adaptée à la plateforme visée (web : landing page, application SaaS, back-office/admin) et de façon équivalente pour le mobile (iOS/Android, conventions Material/HIG) et le desktop (natif macOS/Windows/Linux, Electron/Tauri). Se déclenche dès qu'un nouveau projet d'interface démarre, ou que l'utilisateur demande une palette de couleurs, un thème, un "look", un moodboard, ou pose des questions comme "quel style pour cette landing", "je veux un thème futuriste", "quelles couleurs pour mon appli mobile/desktop". Produit une recommandation texte (palette avec rôles, typographie, justification) et un artifact HTML de moodboard montrant la palette et des composants types en clair/sombre.
---

# Direction de design UI

Ce skill couvre les trois plateformes (web, mobile, desktop) avec la même logique de
décision : identifier le type d'appli → clarifier le style si ambigu → construire une
palette et une typographie justifiées → adapter aux conventions de la plateforme →
livrer une recommandation texte + un moodboard visuel.

## Étape 1 — identifier plateforme et type d'appli

| Plateforme | Types courants | Incidence sur le design |
|---|---|---|
| Web | Landing page | Marketing, hero fort, CTA très contrasté, palette qui peut être audacieuse |
| Web | Application SaaS / produit | Palette plus calme et neutre, surfaces multiples (sidebar, panneaux), densité d'info moyenne |
| Web | Back-office / admin | Utilitaire, palette sobre, forte densité (tableaux, formulaires), priorité à la lisibilité |
| Mobile | iOS / Android (natif ou cross-platform) | Respect des conventions Human Interface Guidelines (iOS) ou Material Design 3 (Android), zones tactiles ≥ 44pt/48dp |
| Desktop | Natif (macOS/Windows/Linux) ou Electron/Tauri | Chrome natif, états hover (souris), layouts plus denses qu'en mobile, raccourcis clavier visibles |

Si le contexte ne précise pas la plateforme ou le type, demander plutôt que de
supposer — c'est une décision structurante qui conditionne toute la suite (cf. la
règle générale du CLAUDE.md : ne pas trancher silencieusement sur nommage/structure).

## Étape 2 — clarifier le style si ambigu

Si l'utilisateur n'a pas donné de direction claire (futuriste, minimal, corporate,
ludique...), utiliser AskUserQuestion avec des options concrètes plutôt que de choisir
à sa place, par exemple :

- **Futuriste / tech** — dark-first, accents néon ou dégradés, glassmorphism, police
  géométrique ou monospace pour les accents
- **Minimal / épuré** — beaucoup de blanc/espace négatif, palette réduite, typographie
  humaniste
- **Corporate / professionnel** — palette sobre, contrastes mesurés, typographie
  classique, peu d'effets
- **Ludique / friendly** — couleurs vives, formes arrondies, illustrations, ton
  décontracté

Ne pas imposer un style par défaut sans le signaler : si le contexte suggère clairement
un style (ex: "SaaS B2B pour la finance" → plutôt corporate), le proposer comme
suggestion argumentée, mais laisser l'utilisateur trancher.

## Étape 3 — construire la palette

- Partir d'une couleur de base (couleur de marque si elle existe, sinon en proposer
  une cohérente avec le style choisi) et en dériver les rôles : primaire, secondaire,
  accent, neutre/surface, fond, texte, plus les couleurs sémantiques (succès, alerte,
  erreur, info).
- Toujours livrer une variante claire **et** une variante sombre — le CLAUDE.md exige
  une vérification visuelle en clair/sombre pour toute tâche UI, donc la proposition
  doit couvrir les deux dès le départ.
- Vérifier le contraste texte/fond (au minimum WCAG AA) plutôt que de proposer des
  couleurs esthétiques mais peu lisibles.

## Étape 4 — typographie

- Web : polices variables ou Google Fonts possibles, pairing titres/texte selon le
  style (ex: géométrique + monospace pour futuriste, humaniste sobre pour corporate).
- Mobile : privilégier la police système (SF Pro sur iOS, Roboto/variable sur Android)
  sauf identité de marque forte qui justifie une police custom.
- Desktop : polices système natives par OS (SF Pro macOS, Segoe UI Windows, police par
  défaut de la distribution sur Linux) sauf branding fort.

## Étape 5 — spécificités par plateforme

- **Web** : la landing peut se permettre plus d'audace visuelle (une seule page, forte
  intention marketing) ; le SaaS doit rester lisible sur la durée (usage répété,
  fatigue visuelle) ; le back-office privilégie la densité d'info sur l'esthétique.
- **Mobile** : respecter les patterns de navigation attendus (tab bar / bottom nav),
  les tailles de zones tactiles, et le comportement attendu en light/dark mode système.
- **Desktop** : prévoir les états hover (absents en tactile), une densité d'info plus
  proche du web que du mobile, et la cohérence avec les conventions natives de l'OS
  ciblé si l'appli doit s'y sentir "chez elle".

## Étape 6 — livrables

Toujours produire les deux :

1. **Recommandation texte** : tableau rôle → couleur (hex) → usage, typographie
   retenue, et une justification courte (pourquoi ce choix pour ce type d'appli/ce
   style), pour le mode clair et le mode sombre.
2. **Artifact HTML de moodboard** : swatches de la palette, spécimen typographique, et
   2-3 composants représentatifs (bouton, carte, champ de saisie ou nav) illustrés en
   clair et en sombre. Charger le skill `artifact-design` avant de publier cet
   artifact (contrat standard des artifacts de cet environnement).

## Ce qu'il ne faut pas faire

Ne pas se rabattre sur des associations toutes faites ("futuriste = violet/cyan néon
obligatoire", "corporate = bleu marine obligatoire"). Partir du contexte réel de
l'utilisateur (secteur, cible, contraintes de marque existantes) et proposer une
direction argumentée, pas un cliché.

**Prochaine étape** : /architect (si pas encore de PRD) ou /plan — la direction retenue alimente DESIGN.md / les tâches UI.
