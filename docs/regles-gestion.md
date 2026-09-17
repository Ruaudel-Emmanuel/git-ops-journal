# Politique de gestion du compte GitHub

> Référentiel des règles appliquées au compte. À tenir à jour à chaque changement.
> (Anonymisé : `<user>` = le propriétaire du compte.)

## 1. Typologie des dépôts

| Catégorie | Critère | Traitement |
|---|---|---|
| **Actifs** | commits réguliers ou usage courant | maintenus, visibles dans le radar de cadence |
| **Dormants à garder** | valeur de référence / portfolio | maintenus mais surveillés (cadence 14 j) |
| **Archivés** | abandonnés, tests, doublons | archivés (read-only), sortent des automatisations |

Règles d'hygiène :
- Pas de doublons de nom (précédent : 2 familles de doublons détectées et nettoyées).
- Tout nouveau dépôt **doit avoir une description** (automatisation de vérification/correction).
- Tout projet porté par le VPS est versionné dans un dépôt privé avec README.

## 2. Cadence de commit attendue (radar de supervision)

Deux niveaux de fenêtre, codés dans l'automatisation « radar de cadence » :

| Niveau | Fenêtre | Exemples de dépôts |
|---|---|---|
| Niveau 1 (sensible) | **7 jours** | site vitrine, portfolio, ops du serveur, photo |
| Niveau 2 (maintenance) | **14 jours** | API, outils internes, projets secondaires |

Un dépôt sans commit au-delà de sa fenêtre apparaît en section « ⏳ À committer » du
rapport quotidien de supervision, avec conseils (2-3 max) générés par l'assistant IA.

## 3. Mises à jour de dépendances

- Hebdomadaire (dimanche 08h30, puis lundi 22h00 après ajout des PR) : détection des
  dépendances (`package.json` / `requirements.txt`) dépassées → branche `deps/maj-auto-<date>`
  → **PR automatique** vers la branche par défaut.
- `main` n'est jamais poussé directement ; tout passe par PR relue par `<user>`.
- Préversions ignorées ; préfixes de version (`^`, `~`) préservés.

## 4. Tokens d'accès (PAT)

- Un seul PAT **fine-grained** actif, stocké dans la base n8n (nœud config du workflow de
  push). Permissions minimales : Contents read/write + Pull requests read/write + Administration
  (archivage) sur les dépôts concernés.
- Jamais de token en session, jamais dans un fichier commité ni dans le journal.
- Un token exposé en clair lors d'une inspection = **rotation immédiate** + révocation de
  l'ancien sur GitHub (Settings → Developer settings → Fine-grained tokens).
- Bonne pratique de vérification : le token commence par `github_pat_` ou `ghp_` (les clés
  Tally commencent par `tly-`, Stripe par `whsec_` — ne pas confondre).

## 5. Hygiène

- Revue trimestrielle : dépôts à archiver, doublons, README à jour.
- Issue de test créée pendant un diagnostic → fermer, ne pas laisser traîner.
- Chaque nouvelle automatisation → doc dans `docs/automatisations.md` + export workflow
  versionné dans le repo ops (secrets masqués, double vérification avant push).
