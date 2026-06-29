# Strategie — Cockpit SEO multi-clients (MCP Google Search Console + GA4)

Ce dépôt connecte Claude aux données SEO de **tous vos clients** via deux serveurs
MCP, pour analyser la performance, suivre les roadmaps et construire vos stratégies.

## Architecture

```
Claude (vous + moi)
        │
        ├── MCP « gsc »  → Google Search Console API   (mcp-server-gsc)
        └── MCP « ga4 »  → Google Analytics Data API    (analytics-mcp, officiel Google)
                                   │
                    un seul compte de service Google Cloud
                                   │
        autorisé en lecture sur chaque propriété GSC + GA4 de vos clients
```

- **`gsc`** — [`mcp-server-gsc`](https://github.com/ahonn/mcp-server-gsc) (Node.js).
  Outil principal : `search_analytics` (impressions, clics, CTR, position par
  requête / page / pays / device, sur n'importe quelle plage de dates).
- **`ga4`** — [`google-analytics-mcp`](https://github.com/googleanalytics/google-analytics-mcp)
  (officiel Google, Python). Outils : `get_account_summaries`,
  `get_property_details`, `run_report`, `run_funnel_report`,
  `run_realtime_report`, `get_custom_dimensions_and_metrics`,
  `list_google_ads_links`.

Les deux partagent la variable `GOOGLE_APPLICATION_CREDENTIALS` → **un seul
compte de service** pour toute l'agence.

## Installation

> **Authentification recommandée : OAuth avec votre propre compte Google** — voir
> **[`docs/AUTH.md`](docs/AUTH.md)**. Elle utilise votre accès existant à toutes
> les propriétés clients ; pas besoin de faire ajouter un compte de service par
> chaque client (ce que Search Console refuse de toute façon dans son UI).

1. Auth : `gcloud auth application-default login --scopes=…webmasters.readonly,…analytics.readonly,…cloud-platform` (commande complète dans `docs/AUTH.md`).
2. `cp .mcp.json.example .mcp.json` (Option A déjà préremplie : projet `agence-seo-mcp`, fichier ADC).
3. `cp clients/clients.example.json clients/clients.json` et remplir le registre.
4. Relancer Claude Code et vérifier (`get_account_summaries`).

L'alternative « compte de service » reste documentée dans
[`docs/SETUP-GOOGLE-CLOUD.md`](docs/SETUP-GOOGLE-CLOUD.md).

### Prérequis
- Node.js 18+, Python 3.10+ avec `pipx`.

## Le registre clients

`clients/clients.json` est la source de vérité de l'agence : il fait le lien entre
le **nom** d'un client et ses identifiants techniques (`gsc_site_url`,
`ga4_property_id`) ainsi que son contexte (marché, objectifs SEO, concurrents).

Quand vous me dites *« analyse le SEO du client X »*, je lis ce registre, je
résous les bons identifiants, et j'interroge les serveurs MCP automatiquement.

> ⚠️ `clients/clients.json` et les fichiers `credentials/*.json` sont **ignorés
> par git** : ni vos identifiants ni vos données clients ne sont poussés. Seuls
> les fichiers `*.example` sont versionnés.

## Comment on travaille ensemble

1. **Vous me donnez votre vision SEO et vos livrables types** → je les consigne
   dans `methodologie/` pour m'y conformer.
2. Vous me demandez une analyse, un audit, une roadmap pour un client.
3. Je tire les données live (GSC + GA4), je croise avec le contexte du registre,
   et je produis l'analyse / la stratégie au format de vos livrables.

Voir **[`CLAUDE.md`](CLAUDE.md)** pour le mode opératoire détaillé.

## Sécurité
- Aucun identifiant n'est stocké dans le dépôt (voir `.gitignore`).
- Les serveurs MCP n'ont qu'un accès **lecture seule** aux données.
- Le fichier `.mcp.json` doit être activé manuellement par vous (jamais committé par moi).
