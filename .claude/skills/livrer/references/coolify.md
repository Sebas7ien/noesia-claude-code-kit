# Coolify (auto-hébergé) — procédure deploy (hosting = coolify)

> **Lis ce fichier uniquement si** `/livrer` Étape 1.2 détecte `hosting: Coolify`. Coolify est un PaaS auto-hébergé (sur ton VPS) : même confort que Vercel (deploy au push, previews, TLS auto), mais sur ton serveur.

## Pré-requis (à confirmer, jamais supposés)

- Une instance Coolify joignable (`COOLIFY_URL`) et un serveur déjà ajouté dedans.
- L'application créée dans Coolify (source = ton repo GitHub, build pack = Nixpacks, Dockerfile ou Docker Compose).
- Pour déclencher un deploy depuis la CLI : l'**API activée** (sur une instance auto-hébergée, un admin doit l'activer dans les réglages ; elle l'est déjà sur Coolify Cloud) et un token créé dans *Keys & Tokens > API tokens* avec la permission **`deploy`** (ou `root`). Le token, copié en entier (`id|secret`), va dans `COOLIFY_TOKEN` dans un `.env` **gitignoré** — jamais en clair dans le repo, les logs ni la conversation. Un token par équipe/intégration, avec une date d'expiration.
- `COOLIFY_URL` = URL de ton instance (l'API est sous `$COOLIFY_URL/api/v1`).

## Procédure

1. **Auto-deploy au push (recommandé)** : si l'app est connectée via GitHub App ou webhook, `git push origin main` déclenche le build. Vérifie dans Coolify → l'app → *Deployments*.
2. **Deploy manuel via API** (si pas d'auto-deploy) — `POST /api/v1/deploy` (vérifié dans la doc officielle ; les paramètres `uuid`, `tag`, `force` passent en query ou en corps JSON) :
   ```bash
   curl -sS -X POST -H "Authorization: Bearer $COOLIFY_TOKEN" \
     "$COOLIFY_URL/api/v1/deploy?uuid={app-uuid}&force=false"
   ```
   - `uuid` : UUID de l'app (visible dans l'URL de l'app dans Coolify) ; `tag` : alternative pour déployer plusieurs ressources ; `force=true` : rebuild sans cache.
   - Réponse 200 : `{"deployments":[{"message":…,"resource_uuid":…,"deployment_uuid":…}]}`. Erreurs : 401 (`Invalid token.` / `Unauthenticated.`) → token absent, mal copié ou sans permission `deploy`.
   - Suivi : `curl -sS -H "Authorization: Bearer $COOLIFY_TOKEN" "$COOLIFY_URL/api/v1/deployments/{deployment_uuid}"` ; lis le champ `status` tel que renvoyé (les valeurs ne sont pas documentées, ne présume pas de leur libellé) et recoupe avec l'onglet *Deployments* du dashboard.
   - Une version ancienne de Coolify peut différer (l'ancien endpoint était un `GET`) : si 404/405, vérifie la version et la doc de ton instance.
3. Variables d'environnement de prod : à saisir dans Coolify → l'app → *Environment Variables* (jamais committées).
4. Attends la fin du déploiement (statut *Running* + healthcheck vert dans Coolify) avant le smoke test ; collecte l'URL (`URL_DEFAUT`) affichée par Coolify (domaine généré ou domaine saisi).

Pour chaque commande : affiche, demande *"J'exécute ? (oui / modifie / skip)"*, attends réponse. Propose d'écrire la commande de deploy dans une section `## Déploiement` du CLAUDE.md.

## Domaine custom côté Coolify

Quand Étape 3.5 du SKILL.md demande un domaine custom et que l'hosting est Coolify :

Affiche : *"Dans Coolify → l'app → *Domains*, saisis `https://{URL_CIBLE}` et redéploie. Côté DNS, crée un enregistrement **A** `{URL_CIBLE}` → l'IP publique de ton serveur (`DNS_TYPE=A`, `DNS_TARGET={IP du serveur}`). Le certificat Let's Encrypt est émis automatiquement une fois le DNS propagé (ports 80/443 ouverts)."* Puis continue avec Étape 3.5.4 (DNS registrar-aware).
