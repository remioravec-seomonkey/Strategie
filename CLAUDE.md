# Mode opératoire — Agence SEO (Claude)

Ce dépôt est le cockpit SEO multi-clients de l'agence. Voici comment travailler.

## Données disponibles (serveur MCP)

- **`gsc`** (Search Console, via `suganthan-gsc-mcp`, OAuth navigateur). Données
  par `siteUrl`, plage de dates, et dimensions (query, page, country, device,
  date, searchAppearance) : impressions, clics, CTR, position. Pour les requêtes
  non-marque, filtrer/exclure les termes de marque.
- **GA4** : pas branché (nécessite Python). Si une analyse a besoin de
  conversions/sessions, le signaler à l'utilisateur plutôt que d'inventer.

## Résolution d'un client

1. Lire `clients/clients.json` (s'il n'existe pas, demander à l'utilisateur de le
   créer à partir de `clients/clients.example.json`).
2. Faire correspondre la demande (« client X », un domaine, un slug) à une entrée.
3. Utiliser `gsc_site_url` pour les appels `gsc`. Tenir compte du contexte
   (`objectifs_seo`, `marche`, `concurrents`) dans l'analyse.
4. En cas d'ambiguïté sur le client visé, demander avant de tirer les données.

## Plages de dates
- Par défaut : 28 derniers jours vs 28 précédents (comparaison), sauf demande.
- GSC a ~2-3 jours de latence : ne pas inclure les jours les plus récents comme
  référence fiable.

## Documents de travail (le cœur du cockpit)
4 dossiers, dans l'ordre du travail SEO :
1. `documents/01-strategie/` — vision : « où on va, pourquoi ».
2. `documents/02-roadmaps/` — découpage dans le temps : « quoi, quand ».
3. `documents/03-controle-qualite/` — vérifications : « est-ce bien fait ».
4. `documents/04-plan-action/` — tâches concrètes : « qui fait quoi ».

Chaque dossier a un modèle `_modele-*.md`. Pour un livrable client : copier le
modèle, le renommer (ex. `clientX-2026-T3.md`), et le remplir avec les data GSC.

## Méthodologie & livrables
- La vision SEO et les exemples de livrables de l'utilisateur vivent dans
  `methodologie/`. **Les lire avant de produire un livrable** et s'y conformer
  (structure, ton, indicateurs privilégiés). Caler les modèles `documents/` sur
  ces exemples quand l'utilisateur les fournit.
- Si `methodologie/` est vide, demander à l'utilisateur sa vision / un exemple de
  livrable avant de produire une stratégie « générique ».

## Garde-fous
- Accès **lecture seule** : ne jamais tenter de modifier les propriétés Google.
- Ne jamais committer `credentials/*.json` ni `clients/clients.json` (gitignored).
- Ne jamais committer `.mcp.json` ni `gsc-oauth-secrets.json` (secrets locaux).
- Citer les chiffres avec leur source (GSC) et leur plage de dates.
