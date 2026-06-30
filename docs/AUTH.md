# Authentification GSC — sans Python, sans gcloud

## Le principe

On connecte Search Console **en tant que vous** (OAuth), via votre navigateur.
Vous avez déjà accès en lecture à toutes les propriétés de vos clients : on
réutilise cet accès. Rien à demander aux clients, aucun compte de service.

Le serveur `suganthan-gsc-mcp` tourne **uniquement avec Node (`npx`)** — déjà
présent puisque Claude Code l'utilise. **Aucun Python, aucun gcloud, aucun
fichier ADC à fabriquer.**

## A → Z

### 1. Créer l'identifiant OAuth (dans le navigateur)

Tout se fait sur <https://console.cloud.google.com/> avec votre compte
`remi.oravec@seo-monkey.fr`, dans le projet existant **`agence-seo-mcp`**
(l'API Search Console y est déjà activée).

1. *APIs & Services → Écran de consentement OAuth* (OAuth consent screen) :
   - Type : **External** (Externe).
   - Nom de l'app : `GSC MCP` · e-mail d'assistance : votre e-mail.
   - Ajoutez votre e-mail comme **utilisateur test**.
   - **Recommandé** : cliquez **« Publier l'application » / "Publish app"**
     (statut *In production*). Sinon le jeton expire tous les 7 jours et il faut
     se reconnecter. En production non vérifiée, un écran d'avertissement
     apparaîtra à la connexion → cliquez *Paramètres avancés → Continuer* (c'est
     votre propre app, c'est normal).
2. *APIs & Services → Identifiants (Credentials) → Créer des identifiants →
   ID client OAuth*.
   - Type d'application : **Application de bureau** (Desktop app).
   - Nom : `GSC MCP OAuth` → **Créer**.
3. **Téléchargez le fichier JSON** et enregistrez-le dans votre dossier
   personnel, par exemple : `~/gsc-oauth-secrets.json`.

### 2. Activer le serveur dans le dépôt

```bash
cp .mcp.json.example .mcp.json
```

Dans `.mcp.json`, vérifiez que `GSC_OAUTH_SECRETS_FILE` pointe sur le fichier
téléchargé (par défaut `${HOME}/gsc-oauth-secrets.json`) et mettez un de vos
sites dans `GSC_SITE_URL` (ex. `sc-domain:monclient.com`). Pour plusieurs
clients, voir la note `_multi_clients` du fichier.

### 3. Première connexion

Relancez Claude Code dans ce dossier, puis demandez par exemple :
> « Donne-moi les top requêtes GSC des 28 derniers jours pour `<un de vos sites>` »

À la première requête, **une fenêtre Google s'ouvre** → connectez-vous, cliquez
**Autoriser**. Le jeton est mémorisé : plus besoin de se reconnecter ensuite.

## Format des sites (`siteUrl`)
- Propriété de **domaine** : `sc-domain:exemple.com`
- Propriété d'**URL** : `https://www.exemple.com/`

## Et les propriétés où vous n'avez AUCUN accès ?
Si pour un client vous n'êtes ni propriétaire ni utilisateur en GSC, aucune
méthode technique ne contourne ça : le propriétaire doit accorder un accès en
lecture à **votre** compte Google. C'est la seule étape qui dépend d'un tiers.

## Ajouter GA4 plus tard ?
GA4 (`analytics-mcp`) nécessite Python. Tant que ce n'est pas dispo, on reste sur
GSC, qui couvre l'essentiel du pilotage SEO (requêtes, clics, impressions,
positions, pages).
