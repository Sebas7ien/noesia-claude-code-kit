# VPS + Docker (auto-hébergé, sans PaaS) — procédure deploy (hosting = vps)

> **Lis ce fichier uniquement si** `/livrer` Étape 1.2 détecte `hosting: VPS` (ou « serveur perso », « Docker sur VPS »). Pour un VPS qui exécute Coolify → `references/coolify.md`. Pour Hostinger spécifiquement → `references/hostinger.md`.

## Informations à demander (et noter dans `## Déploiement` du CLAUDE.md)

- Hôte SSH (`user@host`), chemin de l'app sur le serveur, clé SSH déjà configurée (jamais de mot de passe ni de clé dans la conversation).
- Mode de déploiement : `docker compose` sur le serveur (`git pull` + build), ou image poussée sur un registre (GHCR / Docker Hub) puis `docker compose pull`.
- Reverse proxy déjà en place (Caddy, Traefik, Nginx) pour le TLS ; sinon le proposer (Caddy = HTTPS automatique avec un simple `Caddyfile`).

## Procédure (commandes affichées, jamais auto-exécutées)

```bash
# 1) Pré-vol local : le build Docker passe-t-il ?
docker compose build

# 2) Déploiement sur le serveur
ssh {user}@{host} 'cd {chemin} && git pull --ff-only && docker compose up -d --build'

# 3) Vérification
ssh {user}@{host} 'cd {chemin} && docker compose ps && docker compose logs --tail=50'
```

Variante registre : `docker build -t ghcr.io/{org}/{app}:{tag} . && docker push …` puis `ssh … 'docker compose pull && docker compose up -d'`.

Garde-fous : fichier `.env` de prod **sur le serveur uniquement** ; sauvegarde des volumes/BDD avant toute migration destructive ; pas de `docker system prune`, `rm -rf` ni `--force` sans confirmation explicite ; prévoir un moyen de rollback (tag d'image précédent ou commit précédent).

Pour chaque commande : affiche, demande *"J'exécute ? (oui / modifie / skip)"*, attends réponse. `URL_DEFAUT` = `http://{IP}` ou le domaine déjà servi par le reverse proxy.

## Domaine custom côté VPS

Affiche : *"Crée un enregistrement **A** `{URL_CIBLE}` → l'IP publique du serveur (`DNS_TYPE=A`, `DNS_TARGET={IP}`), puis déclare le domaine dans ton reverse proxy (ex : bloc `{URL_CIBLE} { reverse_proxy app:3000 }` dans le Caddyfile) ; le certificat est émis automatiquement une fois le DNS propagé (ports 80/443 ouverts)."* Puis continue avec Étape 3.5.4.
