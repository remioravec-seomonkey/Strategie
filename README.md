# Strategie — Cockpit SEO multi-clients (MCP Google Search Console)

Ce dépôt connecte Claude aux données SEO de **tous vos clients** via Google
Search Console, pour analyser la performance, suivre les roadmaps et construire
vos stratégies. **Sans Python, sans gcloud** : Node + connexion Google navigateur.

## Architecture

```
Claude (vous + moi)
        │
        └── MCP « gsc »  → Google Search Console API   (suganthan-gsc-mcp, Node)
                                   │
                  OAuth navigateur, en tant que VOUS
                                   │
              vos propriétés GSC clients (accès en lecture existant)
```

- **`gsc`** — [`suganthan-gsc-mcp`](https://www.npmjs.com/package/suganthan-gsc-mcp)
  (Node.js, `npx`). Données : impressions, clics, CTR, position par
  requête / page / pays / device, sur n'importe quelle plage de dates.
- **GA4** : optionnel, ajouté plus tard (nécessite Python — voir `docs/AUTH.md`).

Auth : **OAuth dans le navigateur**, avec votre propre compte Google. Aucun
compte de service à faire ajouter par les clients.

## Installation — simple, sans Python ni gcloud

> Auth **OAuth navigateur** : on se connecte en tant que vous, votre accès
> existant couvre déjà toutes les propriétés clients. Détail A→Z dans
> **[`docs/AUTH.md`](docs/AUTH.md)**.

1. Créez un **ID client OAuth « Application de bureau »** dans Google Cloud
   (projet `agence-seo-mcp`) et téléchargez le JSON (`~/gsc-oauth-secrets.json`).
2. `cp .mcp.json.example .mcp.json` (vérifiez le chemin du JSON et un `GSC_SITE_URL`).
3. `cp clients/clients.example.json clients/clients.json` et remplir le registre.
4. Relancer Claude Code → 1re requête GSC → **fenêtre Google → Autoriser**. ✅

### Prérequis
- **Node.js 18+** uniquement (déjà là si Claude Code tourne). Pas de Python.

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
