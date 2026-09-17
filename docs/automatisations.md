# Automatisations GitHub — inventaire

> Tout ce qui touche automatiquement au compte GitHub, où ça tourne, ce que ça fait.
> ⚠️ Anonymisé : aucun secret ici. Les tokens vivent dans la base n8n ou dans des fichiers
> root-only du VPS (`<pat>` = PAT fine-grained, `<chat_id>` = canal de supervision).

---

## 1. Push vers GitHub via webhook n8n — « Push GitHub »

- **Où** : workflow n8n `Push GitHub` (webhook POST `/webhook/github-push`).
- **Rôle** : pousser un projet local (`~/projects/<dossier>`) vers GitHub **sans jamais
  transmettre le PAT en session** : le token est stocké dans un nœud Config du workflow et
  extrait de la base au besoin.
- **Sécurité** : le script hôte (`projects/scripts/github-push.sh`, monté dans le conteneur)
  crée le repo s'il n'existe pas (privé par défaut), attache le remote sans persister le
  token dans `.git/config`, nettoie toute sortie contenant le token.
- **Auth webhook** : header `X-Auth-Token` = même valeur que le champ token du nœud Config.

## 2. Descriptions auto + dépendances + PR — « GitHub — descriptions auto »

- **Déclencheurs** : cron quotidien 07h45 (descriptions) + hebdo lundi 22h00 (dépendances).
- **Descriptions** : pour chaque dépôt actif sans description → génération par IA (style
  conforme aux exemples du prompt) → PATCH de la description GitHub. Aucune écrasement
  d'une description existante.
- **Dépendances** (dimanche 08h30 → déplacé lundi 22h00) :
  1. Détection `package.json`/`requirements.txt` via API Git (tree),
  2. Comparaison aux dernières versions stables npm/PyPI (préversions ignorées),
  3. Création branche `deps/maj-auto-<date>` + 1 commit de maj (API blob→tree→commit→ref,
     `main` jamais touché, préfixes `^`/`~` préservés),
  4. **Ouverture automatique d'une PR** branche → défaut (titre + corps avec liste à cocher),
  5. Récap sur le canal de supervision (🌿 PR ouverte / ✅ à jour / ➖ rien / ↩️ déjà fait / ⚠️ erreur).
  Si la branche existe déjà sans PR → la PR est ouverte au run suivant.
- **Pièges rencontrés (importants)** :
  - `httpRequest({json:false})` parse automatiquement les JSON → `package.json` arrivait en
    objet ; fix = récupérer le texte brut.
  - `encodeURIComponent` sur un package npm **scopé** casse l'URL du registre.
  - Le cron d'un trigger schedule n8n s'appelle `expression` (pas `cronExpression`).
  - Le PAT fine-grained doit avoir la permission **Pull requests: Read and write**, sinon
    les PR partent en 403 (l'issue et le branch restent OK).

## 3. Radar de cadence — intégré à l'agent de supervision Telegram

- **Où** : workflow n8n `Agent Telegram` (nœud « Prépare le résumé »), cron quotidien 07h00.
- **Rôle** : liste les dépôts actifs (per_page=100), calcule les dépassements de fenêtre
  Niveau 1 (7 j) / Niveau 2 (14 j) → section « Cadence — dépassements » (⚠️) dans le rapport
  quotidien, + section « 💡 Conseils » (2-3 max) générée par l'IA locale.
- Interdits au modèle : se baser sur un rapport antérieur, langue autre que le français.

## 4. Rapport hebdomadaire de CE dépôt — `github-weekly-report`

- **Où** : VPS, hors n8n — script `/usr/local/bin/github-weekly-report.sh` (root 700) +
  timer systemd `github-weekly.timer` (**vendredi 19h00 Europe/Paris**).
- **Flux** :
  1. Extraction du PAT de la base n8n **directement dans une variable shell** (jamais
     affichée, jamais sur disque) ;
  2. Collecte API : `/user/events` (7 derniers jours) — PushEvent (commits/dépôt),
     PullRequestEvent (ouvertes/fusionnées/fermées), CreateEvent/DeleteEvent (branches,
     dépôts), IssuesEvent, ReleaseEvent ;
  3. Différentiel des dépôts vs l'état de la semaine précédente
     (`/var/lib/github-weekly/repos-state.json`) → nouveaux dépôts, dépôts archivés ;
  4. Génération de `semaines/AAAA-Sww.md` (anonymisé) ;
  5. Commit + push sur CE repo (token uniquement en argument du push, jamais persisté) ;
  6. Récap court sur le canal de supervision.
- **Dépendance** : le clone local du repo dans `~/projects/git-ops-journal`.

## 5. Archives d'export des workflows

- Tous les workflows n8n touchant GitHub sont exportés (secrets masqués) dans le repo
  **ops** du serveur (`<user>/…vps-ops`, privé) avec README documentant les pièges.
- Double vérification avant chaque push : grep sur `ghp_`, `github_pat_`, `whsec_`, `tly-`,
  tokens inline.
