# 🗓️ Une semaine d'automatisations — vue d'ensemble

> Énumération de tout ce qui tourne automatiquement sur une semaine type
> (n8n + timers systemd), avec une courte description. Heures **Europe/Paris**.
> Anonymat : aucun secret, aucun identifiant (voir README).

## Calendrier hebdomadaire

| Quand | Automatisation | Type | Description |
|---|---|---|---|
| **toutes les 15 min** | Watchdog VPS | timer systemd → webhook n8n « Alertes VPS » | Vérifie conteneurs, HTTPS, disque/RAM, fail2ban, backup ; alerte **uniquement si anomalie** (le PC éteint n'est pas une anomalie). Le webhook n8n vérifie un token puis relaie sur Telegram. |
| **chaque jour 07:00** | Agent Telegram | n8n (cron + Telegram) | Bot IA local (modèle 7b sur le VPS) + **radar de cadence GitHub** : dépôts actifs, dépassements de fenêtre N1 7 j / N2 14 j (« ⏳ À committer »), 2-3 conseils du jour. |
| **chaque jour 07:45** | GitHub — descriptions auto | n8n (cron) | Génère (IA) une description pour chaque dépôt actif qui n'en a pas, et l'applique via l'API. Ne modifie jamais une description existante. Récap Telegram. |
| **chaque jour 08:00** | Rapport métriques VPS | timer systemd | Rapport quotidien Telegram : uptime, RAM, disque, conteneurs, HTTPS, fail2ban, état du backup + PC, ollama, netdata. |
| **chaque jour 09:00 / 15:00 / 21:00** | Diffusion Actus Discord | n8n (cron) | Lit les flux RSS d'une base Airtable, déduplique (1 appel groupé), envoie les nouveaux articles en embeds Discord (avec image), marque envoyé (1 appel groupé). Anti-flood + pause auto 24 h si quota atteint. |
| **chaque jour 09:30 / 15:30 / 21:30** | Diffusion Photos Discord | n8n (cron) | Idem pour une base de flux photo : embeds violets, image extraite par photo, marquage groupé. |
| **lundi 22:00** | GitHub — dépendances + PR | n8n (cron hebdo) | Pour chaque dépôt actif : détecte les dépendances dépassées (npm/PyPI), crée une branche `deps/maj-auto-<date>` avec les mises à jour, **ouvre une PR automatique** (jamais de push direct sur la branche par défaut). Récap Telegram (🌿 PR / ✅ à jour / ⚠️ erreur). |
| **vendredi 19:00** | Rapport hebdo GitHub | timer systemd (ce repo) | Génère `semaines/AAAA-Sww.md` : commits par dépôt, PR, branches, issues, dépôts créés/archivés sur 7 jours → commit + push automatique ici + résumé Telegram. |
| **dimanche 12:00** | Rappel backup | timer systemd | Rappel Telegram : PC joignable ? dépôt de sauvegarde connecté ? âge du dernier snapshot. |
| **dimanche 19:15** | Déchargement des modèles IA | timer systemd | Décharge les modèles ollama de la RAM avant le backup. |
| **dimanche 19:30** | Sauvegarde complète | timer systemd | Snapshot chiffré (volumes Docker, home, configs) vers le dépôt sur le PC personnel (LAN/Tailscale). À la demande aussi via commande Telegram. |

## Événementiels (webhooks — pas d'horaire)

| Automatisation | Déclencheur | Description |
|---|---|---|
| Push GitHub | webhook n8n | Pousse un projet local vers GitHub sans exposer de token en session (création de repo incluse). Utilisé par l'assistant. |
| Stripe — alerte paiement | webhook Stripe | Alerte Telegram à chaque paiement confirmé, **signature vérifiée** (rejet si falsifiée). |
| Tally — alerte message | webhook Tally | Alerte Telegram à chaque soumission du formulaire principal (signature vérifiée). |
| Tally — alerte photographe | webhook Tally | Idem pour le second formulaire (alerte dédiée). |
| Agent Telegram | message reçu | Répond aux messages du bot IA (contexte mémoire, uniquement le chat autorisé). |

## Volume hebdomadaire approximatif

- Exécutions n8n planifiées : ~75 (actus 21 + photos 21 + descriptions 7 + agent 7 + deps 1 + watchdogs via webhook)
- Appels API externes les plus consommés : registres npm/PyPI (1×/sem), API GitHub (quotidien), RSS (6×/j) — quota Airtable tenu à ~6-10 appels/j via appels groupés.
