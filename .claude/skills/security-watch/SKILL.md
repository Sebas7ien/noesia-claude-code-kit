---
name: security-watch
description: "Veille sécurité, analyse de CVE ciblée et audit des dépendances d'un projet (CERT-FR, CISA/KEV, NVD, OSV.dev), filtré sur JS/TS, Python, Kotlin/Android et Docker. Utiliser pour une veille générale ('quoi de neuf en sécu'), une CVE précise ('la CVE-2024-XXXX nous concerne ?') ou un audit de dépendances ('mes packages ont des CVE ?'). Déclenchement manuel uniquement. Ne touche jamais au code : complémentaire à security-review, qui couvre le diff."
---

# Veille et audit sécurité

Ce skill s'active uniquement à la demande explicite — ce n'est pas une veille
automatique en arrière-plan. Il couvre trois usages distincts ; identifier
lequel est demandé avant de commencer (si ambigu, demander).

## Sources de référence

**CERT/CSIRT officiels**
- CERT-FR (ANSSI) — https://www.cert.ssi.gouv.fr — bulletins d'alerte et avis
- CISA (USA) — alertes + KEV catalog (vulnérabilités activement exploitées) — https://www.cisa.gov/known-exploited-vulnerabilities-catalog
- NVD — https://nvd.nist.gov — base CVE officielle US, scores CVSS
- MITRE CVE — https://cve.org — référentiel des identifiants CVE

**Bases de vulnérabilités**
- CVE (identifiant), CVSS (score 0-10), Exploit-DB (PoC publics)
- KEV Catalog (CISA) — priorité de patch : une CVE dans le KEV est confirmée
  exploitée activement, donc toujours à traiter en priorité absolue
- OSV.dev (https://osv.dev, API https://api.osv.dev) — base ouverte
  interrogeable par écosystème (npm, PyPI, Maven/Gradle, Docker/OS packages),
  bien adaptée à l'audit de dépendances en masse. Pas dans la liste de
  sources donnée initialement mais utile pour l'usage 3 ci-dessous — le
  signaler à l'utilisateur la première fois qu'il est utilisé.

**Frameworks de classification**
- MITRE ATT&CK (https://attack.mitre.org) — tactiques/techniques attaquants
- MITRE CWE (https://cwe.mitre.org) — types de faiblesses logicielles
- OWASP Top 10 (https://owasp.org/www-project-top-ten/) — vulnérabilités web

**Outils nécessitant une clé API (souvent indisponibles ici)**
- Shodan, Censys, VirusTotal, MISP — si un de ces outils serait utile mais
  qu'aucune clé n'est configurée, le dire clairement plutôt que d'inventer
  un résultat ou de prétendre y avoir accédé.

**Médias / newsletters** (pour du contexte, jamais comme source primaire de
scoring) : The Hacker News, Bleeping Computer, Krebs on Security, tl;dr sec,
Risky Business.

Utiliser WebFetch/WebSearch pour interroger ces sources en direct — ne jamais
répondre sur une CVE ou une alerte de mémoire sans vérifier l'état actuel
(un score CVSS ou un statut KEV peut changer).

## Filtrage par stack

Sauf demande explicite contraire, prioriser ce qui touche les stacks
réellement utilisées par l'utilisateur : JS/TS (npm/Node), Python (PyPI),
Kotlin/Android (Gradle/Maven), Docker/conteneurs. Le reste (autres langages,
matériel, etc.) peut être mentionné brièvement mais ne doit pas noyer le
signal utile.

## 1. Veille générale ("quoi de neuf")

1. Interroger CERT-FR (derniers avis/alertes) et CISA (alertes + ajouts
   récents au KEV catalog) sur une fenêtre récente (dernière semaine sauf
   précision contraire).
2. Filtrer sur les stacks ci-dessus.
3. Pour chaque item retenu : titre, produit/version concerné, gravité
   (CVSS si connu, sinon la qualification du bulletin), statut KEV
   (exploitée activement ou non), lien vers la source.
4. Trier par criticité (KEV actif > CVSS élevé > reste).
5. Résumer dans le chat (liste courte, pas de mur de texte) **et** écrire le
   rapport complet dans un fichier (voir "Où sont sauvegardés les
   rapports").

## 2. Analyse ciblée d'une CVE ou d'une faille nommée

1. Résoudre l'identifiant : si l'utilisateur donne un CVE-ID, l'utiliser
   directement ; s'il décrit une faille par son nom (ex: "la faille Log4j"),
   la rechercher sur cve.org/NVD pour retrouver le ou les CVE-ID associés.
2. Récupérer via NVD/MITRE : description, score CVSS (et vecteur si
   pertinent), produits/versions affectés, statut KEV, patch ou avis officiel
   disponible.
3. Croiser avec CERT-FR/CISA pour voir si un bulletin spécifique en parle
   (donne souvent le contexte d'exploitation réel).
4. Si la faille concerne une dépendance présente dans un projet de
   l'utilisateur (visible dans le répertoire courant), le signaler
   explicitement et vérifier la version utilisée vs. la version corrigée.
5. Conclure par un verdict clair : criticité, exploitabilité réelle,
   action recommandée (patcher maintenant / surveiller / non concerné).
6. Résumer dans le chat **et** sauvegarder le rapport.

## 3. Audit des dépendances d'un projet

1. Identifier la racine du projet (`git rev-parse --show-toplevel`, sinon le
   répertoire courant).
2. Repérer les manifests présents : `package.json`/`package-lock.json` (JS/TS),
   `requirements.txt`/`pyproject.toml`/`poetry.lock` (Python),
   `build.gradle.kts`/`build.gradle` (Kotlin/Android), `Dockerfile` (images
   de base).
3. Extraire la liste des dépendances directes (et si simple à obtenir, les
   versions résolues via les lockfiles).
4. Interroger OSV.dev (par écosystème : npm, PyPI, Maven, Docker) pour
   chaque dépendance/version, ou par lot si l'API batch le permet.
5. Croiser les résultats avec le KEV catalog CISA pour repérer les CVE
   activement exploitées parmi les dépendances touchées — celles-ci passent
   en priorité absolue peu importe leur score CVSS.
6. Prioriser : KEV actif > CVSS critique/élevé > le reste. Pour chaque
   vulnérabilité retenue, indiquer le chemin de mise à jour (version corrigée
   disponible ou non).
7. Résumer dans le chat **et** sauvegarder le rapport.
8. **Ne jamais bump une dépendance automatiquement** — proposer le diff de
   version, laisser l'utilisateur valider (cohérent avec la règle globale
   "pas de dépendance modifiée sans signalement explicite").

## Où sont sauvegardés les rapports

Dans un dossier `security/` à la racine du projet (git root, ou répertoire
courant si hors dépôt git) — créer le dossier s'il n'existe pas encore :

- Veille générale → `security/veille-YYYY-MM-DD.md`
- Analyse CVE ciblée → `security/cve-<CVE-ID>.md`
- Audit dépendances → `security/audit-YYYY-MM-DD.md`

Chaque rapport commence par la date, les sources consultées, et va droit aux
findings — pas de section vide ni de remplissage.

## Ce que ce skill ne fait jamais

- **Ne jamais exécuter un PoC/exploit** trouvé sur Exploit-DB ou ailleurs
  contre un système, même celui de l'utilisateur, sans autorisation
  explicite et contexte de test clairement légitime.
- **Ne jamais soumettre du code source ou des secrets du projet** à un
  service externe (ex: upload sur VirusTotal) sans validation explicite —
  signaler avant, ne pas le faire en silence.
- **Ne jamais inventer un résultat** pour une source nécessitant une clé API
  non configurée (Shodan, Censys, VirusTotal, MISP) — dire que c'est
  indisponible.
- **Ne jamais modifier une dépendance ou committer un correctif** sans
  validation explicite de l'utilisateur.

**Prochaine étape** : selon les findings, corriger via /plan (jamais de correctif sans validation).
