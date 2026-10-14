# PRD — Rituel

**Niveau** : STANDARD

## 1. Vision

App mobile native qui aide un particulier à tenir 3 à 5 habitudes quotidiennes : une coche par jour,
une série visible, un rappel par habitude. Locale, sans compte, rapide à ouvrir.

## 2. Personas

- **Camille, 32 ans, salariée** — veut boire plus d'eau, lire 15 min, marcher. Douleur : les apps
  existantes sont trop chargées ou demandent un compte.
- **Karim, 45 ans, indépendant** — veut tenir un rituel du matin. Douleur : oublie sans rappel.

## 3. Scope actuel (V1)

### Core
- [ ] Créer / modifier / archiver une habitude (nom, fréquence quotidienne, rappel optionnel)
- [ ] Écran du jour : cocher / décocher en un tap
- [ ] Série en cours et meilleure série par habitude
- [ ] Rappel quotidien par habitude (notification locale)
- [ ] Vue historique mensuelle (calendrier de coches)

### Technique
- [ ] Stockage local (SwiftData sur iOS, Room sur Android)
- [ ] Logique des séries isolée et testée en unitaire
- [ ] Parcours E2E couverts par des flows Maestro
- [ ] Mode sombre et taille de police dynamique

## 4. Hors scope (différé)

- [ ] Compte et synchronisation multi-appareils — V2
- [ ] Widgets écran d'accueil — V2
- [ ] Habitudes à fréquence hebdomadaire ou personnalisée — V2
- [ ] Statistiques avancées / export CSV — V2

## 5. Constraints non-négociables

- Aucune donnée personnelle ne quitte l'appareil en V1 ; aucun SDK d'analytics
- Natif sur chaque plateforme (pas de framework cross-platform)
- Accessibilité : VoiceOver / TalkBack sur l'écran du jour, contrastes AA
- Une plateforme à la fois : iOS pilote, Android après validation

## 6. Success Criteria

- Cocher une habitude : ≤ 2 taps depuis l'ouverture de l'app
- Démarrage à froid < 2 s sur un appareil de milieu de gamme
- Série correcte sur 10 scénarios de test (jour manqué, changement de fuseau, correction de la veille)
- Flows Maestro verts sur iOS puis Android
- Build soumis à TestFlight puis Google Play internal testing sans rejet

## 7. Implementation Phases

- **Phase 1 — Squelette iOS + une habitude de bout en bout** : créer, cocher, persister, série simple
- **Phase 2 — iOS complet** : rappels, historique, archivage, accessibilité, thème sombre
- **Phase 3 — Portage Android** : mêmes fonctionnalités en Kotlin / Compose, mêmes flows Maestro
- **Phase 4 — Distribution** : icônes, captures, politique de confidentialité, TestFlight, Play internal

## 8. Risks & Mitigations

- **Risque** : l'iOS se compile seulement sous macOS + Xcode → **Mitigation** : machine de dev macOS
  confirmée ; Android Studio sur la même machine.
- **Risque** : comptes développeur payants (Apple ~99 $/an, Google Play ~25 $) → **Mitigation** :
  les créer avant la Phase 4, pas au dernier moment.
- **Risque** : permission notifications refusée → **Mitigation** : l'app reste utilisable sans rappel,
  message clair pour réactiver dans les réglages.
- **Risque** : séries faussées par changement de fuseau ou d'heure → **Mitigation** : jour calendaire
  local, tests unitaires dédiés dès la Phase 1.
- **Risque** : rejet store (politique de confidentialité manquante) → **Mitigation** : page de
  confidentialité prête en Phase 4 même sans collecte de données.
