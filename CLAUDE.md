# Mode opératoire — Agence SEO (Claude)

Ce dépôt est le cockpit SEO multi-clients de l'agence. Voici comment travailler.

## Données disponibles (serveurs MCP)

- **`gsc`** (Search Console) — outil `search_analytics`. Paramètres clés :
  `siteUrl`, `startDate`, `endDate`, `dimensions` (query, page, country, device,
  date, searchAppearance), `rowLimit`, filtres. Pour les requêtes non-marque,
  filtrer/exclure les termes de marque.
- **`ga4`** (Google Analytics 4) — `run_report` (dimensions/metrics GA4 :
  sessions, conversions, engagementRate, sessionSource/Medium, landingPage…),
  `run_funnel_report`, `run_realtime_report`, `get_account_summaries`,
  `get_property_details`, `get_custom_dimensions_and_metrics`.

## Résolution d'un client

1. Lire `clients/clients.json` (s'il n'existe pas, demander à l'utilisateur de le
   créer à partir de `clients/clients.example.json`).
2. Faire correspondre la demande (« client X », un domaine, un slug) à une entrée.
3. Utiliser `gsc_site_url` pour les appels `gsc` et `ga4_property_id` pour les
   appels `ga4`. Tenir compte du contexte (`objectifs_seo`, `marche`,
   `concurrents`) dans l'analyse.
4. En cas d'ambiguïté sur le client visé, demander avant de tirer les données.

## Plages de dates
- Par défaut : 28 derniers jours vs 28 précédents (comparaison), sauf demande.
- GSC a ~2-3 jours de latence : ne pas inclure les jours les plus récents comme
  référence fiable.

## Méthodologie & livrables
- La vision SEO et les formats de livrables de l'utilisateur vivent dans
  `methodologie/`. **Les lire avant de produire un livrable** et s'y conformer
  (structure, ton, indicateurs привилégiés).
- Si `methodologie/` est vide, demander à l'utilisateur sa vision / un exemple de
  livrable avant de produire une stratégie « générique ».

## Garde-fous
- Accès **lecture seule** : ne jamais tenter de modifier les propriétés Google.
- Ne jamais committer `credentials/*.json` ni `clients/clients.json` (gitignored).
- Ne jamais committer `.mcp.json` (activation manuelle par l'utilisateur).
- Citer les chiffres avec leur source (GSC vs GA4) et leur plage de dates.
