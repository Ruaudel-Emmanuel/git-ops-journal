# Rotation du PAT GitHub — 2026-09-18

**Contexte** : le PAT fine-grained avait été affiché par erreur dans la session du 18/09
(sortie psql non filtrée lors du rapport RAM) → rotation décidée.

**Ce qui a été fait** :
1. Nouveau token fine-grained généré par l'utilisateur sur GitHub
   (permissions : Contents RW + Pull requests RW + Administration RW, repos : all).
2. Token collé par l'utilisateur dans n8n → workflow « Push GitHub » → nœud « Config (PAT GitHub) ».
3. Propagation SQL par l'assistant (jamais affiché, jamais sur disque) :
   - « GitHub — descriptions auto » (3 occurrences : liste repos, ajoute description, deps/PR)
   - « Agent Telegram » (1 occurrence : GitHub — mes repos)
4. Redémarrage de `n8n_workflow` pour purger le cache mémoire (sinon l'ancien token
   resterait chargé en RAM jusqu'au prochain redémarrage).
5. Ancien token : **à révoquer** par l'utilisateur sur GitHub
   (Settings → Developer settings → Fine-grained tokens → Revoke).

**Règle rappelée** : jamais de token en clair en session ni dans un fichier commité ;
extraction systématique en variable shell ou directement en SQL.
