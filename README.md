# git-ops-journal

> **Journal de gestion d'un compte GitHub personnel + inventaire de ses automatisations.**
> Repo public — mis à jour **automatiquement chaque vendredi soir** par un rapport hebdomadaire
> généré sur le VPS (voir `docs/automatisations.md` § Rapport hebdomadaire).

## 🗓️ Une semaine type, en un coup d'œil

Voir **[docs/workflows-semaine.md](docs/workflows-semaine.md)** : calendrier complet de tout ce qui
tourne chaque semaine (watchdog 15 min, rapports quotidiens, diffusions Discord 3×/j,
dépendances + PR le lundi, rapport hebdo le vendredi, backups le dimanche, webhooks Stripe/Tally).

## 🎯 Objectif

Ce dépôt répond à une seule question : **comment mon GitHub est-il géré, et que font les
automatisations qui le concernent ?** Il sert de référence en cas de perte de mémoire
(suppression d'un workflow, changement de token, reprise après interruption) et de trace
hebdomadaire de l'activité réelle (commits, PR, dépôts créés/archivés).

## 🔒 Règles d'anonymat (à respecter dans TOUT fichier de ce dépôt)

1. **Aucun secret** : pas de PAT/token, pas de clé webhook, pas de mot de passe — même partiel.
   Les secrets vivent uniquement dans la base n8n ou dans des fichiers root-only du VPS.
2. **Pas d'identifiants personnels** : pas de nom réel, pas de pseudo GitHub, pas de nom de
   domaine, pas d'identifiant de chat Telegram. Utiliser des jetons neutres :
   `<user>`, `<domain>`, `<chat_id>`, `<bot>`, `<pat>`.
3. Les noms de dépôts cités en exemple doivent rester génériques (`projet-A`, `projet-B`…).
4. Toute capture ou export workflow déposé ici doit avoir ses secrets remplacés par des
   placeholders **vérifiés** (grep sur les motifs `ghp_`, `github_pat_`, `whsec_`, `tly-`,
   `xoxb`, `Bearer `, URLs avec token).

## 📚 Structure

| Fichier | Rôle |
|---|---|
| `README.md` | ce fichier |
| `docs/regles-gestion.md` | politique de gestion du compte (dépôts actifs/archivés, cadences) |
| `docs/automatisations.md` | inventaire détaillé des automatisations GitHub (flows, déclencheurs, pièges) |
| `docs/workflows-semaine.md` | une semaine type : tout ce qui tourne, quand, et à quoi ça sert |
| `semaines/` | rapports hebdomadaires auto-générés (`AAAA-Sww.md`), un fichier par semaine |

## 🗓️ Rapport hebdomadaire

- Généré **chaque vendredi 19h00 Europe/Paris** par le VPS (script + timer systemd),
  indépendant de n8n : même si n8n est en panne, le journal continue.
- Contenu type : commits poussés (par dépôt), PR ouvertes/fusionnées, branches créées/
  supprimées, dépôts créés/archivés (différentiel), résumé des automatisations passées.
- Le fichier de la semaine est committé automatiquement sur ce repo + récapitulatif court
  envoyé sur le canal de supervision.

## 🔧 Maintenance

- Modifier la politique → `docs/regles-gestion.md` (commit manuel).
- Modifier une automatisation → mettre à jour `docs/automatisations.md` **dans la foulée**.
- Le script du rapport : `/usr/local/bin/github-weekly-report.sh` (root-only sur le VPS,
  contient aucun secret en dur — il extrait le token de la base n8n au moment du run).
