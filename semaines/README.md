# 🗓️ Rapports hebdomadaires

Un fichier par semaine : `AAAA-Sww.md` (ex. `2026-S38.md`), généré automatiquement
chaque **vendredi 19h00 Europe/Paris** par `/usr/local/bin/github-weekly-report.sh` (VPS).

Le fichier contient, sur les 7 derniers jours :
- **Commits** poussés, par dépôt (titres des messages)
- **Pull requests** ouvertes / fusionnées / fermées
- **Branches** créées / supprimées
- **Issues** ouvertes / fermées
- **Dépôts** créés / archivés (différentiel)
- Un résumé de l'activité de gestion

Anonymat : pas de nom d'utilisateur, pas de secrets, pas de domaine — voir README § Règles
d'anonymat.

## Modèle

```markdown
# Semaine AAAA-Sww (du JJ/MM au JJ/MM)

## Commits (N)
- **depot-A** (n) : msg1 ; msg2
- **depot-B** (n) : …

## Pull requests
- Ouvertes : depot-A #1 (deps/maj-auto-…)
- Fusionnées : —
- Fermées sans fusion : —

## Branches
- Créées : depot-A `deps/maj-auto-AAAA-MM-JJ`
- Supprimées : —

## Issues
- Ouvertes/fermées : —

## Dépôts
- Nouveaux : —  |  Archivés : —  |  Total actifs : N

## Gestion
- (notes libres : rotations de token, changements de cadence, incidents)
```
