---
name: tool-security-gate
description: Passe sécurité intégrée au workflow plan/execute. Mode plan (appelé par /plan) ajoute des critères "Fait quand" sécurité aux tâches sensibles ; mode gate (appelé par /execute en fin de phase) applique la checklist du projet au diff, délègue à security-review, et à security-watch si les dépendances ont changé. Verdict ✅/⚠️/❌. Ne PAS utiliser pour une veille CVE seule (→ security-watch) ni pour reviewer un diff hors phase (→ security-review).
---

# Skill tool-security-gate — passe sécurité plan/execute

## Pour quoi faire

Un seul point d'entrée pour la sécurité dans le cycle `/plan` → `/execute`. La checklist n'est jamais recopiée dans `plan` ou `execute` : elle vit ici (baseline) et dans le repo (règles propres au projet).

## Sources de règles

1. **Checklist du projet** : `docs/security-checklist.md` à la racine du repo, si elle existe. Règles propres au projet (modèle multi-tenant, entitlements, canaux…).
2. **Baseline** (toujours appliquée, en complément) :
   - pas de secret en dur, ni dans les logs, ni dans le diff (`.env`, clés, tokens)
   - requêtes SQL paramétrées, jamais de concaténation / f-string dans une requête
   - toute route non publique vérifie l'authentification, puis l'autorisation (rôle / propriétaire de la ressource)
   - entrées utilisateur validées (type, taille, format) ; uploads : taille, type MIME, pas de chemin contrôlé par l'utilisateur
   - webhooks entrants : signature vérifiée
   - erreurs : pas de stacktrace ni de détail interne renvoyé au client
   - pas de `eval`/`exec`/`shell=True`/`dangerouslySetInnerHTML` sur des données non maîtrisées

Si la checklist projet n'existe pas, signale-le dans le verdict ("baseline seule") et suggère de la créer, sans la créer toi-même.

## Mode `plan` (appelé par /plan, Étape 3)

1. Prends la liste des tâches brouillon.
2. Lance l'agent `security-gate` en mode `plan` avec la checklist et les tâches.
3. Intègre ses critères comme une ligne supplémentaire `Sécurité :` sous les tâches concernées (les autres tâches restent inchangées).
4. Les critères comptent dans la limite de 8 tâches : ils s'ajoutent aux "Fait quand", ils ne créent pas de tâche.

## Mode `gate` (appelé par /execute, Étape 2.6)

1. **Périmètre** : fichiers modifiés par la phase (`git diff --name-only` depuis le commit de début de phase, ou `git status` si non commité).
2. Lance l'agent `security-gate` en mode `gate` avec la checklist et le périmètre.
3. Invoque le skill `security-review` sur le même diff pour le raisonnement général (logique, flux de données).
4. **Seulement si** `package.json`, `package-lock.json`, `requirements*.txt`, `pyproject.toml`, `build.gradle*` ou un `Dockerfile` ont changé : lance l'agent `security-watch` en mode audit de dépendances, limité à ces manifests.
5. Fusionne en un verdict :
   - **❌ FAIL** : au moins un finding bloquant (de l'agent, de security-review, ou CVE critique/élevée exploitable). Le handoff vers `/validate` est interdit tant que ce n'est pas corrigé.
   - **⚠️ WARN** : mineurs uniquement. Pas de blocage.
   - **✅ PASS**.
6. Les mineurs : liste-les dans le rapport pour traitement hors phase, sans les corriger dans la phase (règle "pas d'improvisation" de /execute).
7. Un bloquant corrigé → relance uniquement les règles concernées, max 3 itérations avant de demander à l'utilisateur.

## Restitution

```
Sécurité : OK ({n} règles, {n} fichiers})
Sécurité : anomalies mineures — {n} (à traiter hors phase)
Sécurité : KO — {règle} dans {fichier:ligne}
```

Ajoute toujours la ligne "Non vérifié" de l'agent si elle est non vide : un périmètre non couvert n'est pas un PASS silencieux.

## Maintenance de la checklist

Après un incident ou un finding réel hors checklist, propose d'ajouter la règle correspondante à `docs/security-checklist.md` (une ligne, avec un indice de grep). Ne modifie pas le fichier sans accord.

**Prochaine étape** : appelé par /plan (mode plan) et /execute (mode gate), puis /validate.
