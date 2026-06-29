# Authentification — débloquer l'accès aux propriétés clients

## Le problème rencontré

L'approche « compte de service » se heurte à deux murs dans un contexte d'agence :

1. **Search Console n'accepte pas les comptes de service dans son UI** : le
   formulaire *Utilisateurs et autorisations* ne valide que des adresses
   Gmail/Workspace, pas les `…@gserviceaccount.com`.
2. **Vous n'êtes pas admin/propriétaire des propriétés de vos clients** : vous y
   avez accès en lecture, mais vous ne pouvez pas y ajouter un tiers (le compte
   de service). Seuls les propriétaires le peuvent.

## La solution : OAuth avec VOTRE compte (ADC)

Vous avez **déjà** accès en lecture à toutes ces propriétés via votre propre
compte Google. On authentifie donc les serveurs MCP **en tant que vous**, plutôt
qu'avec un robot que personne ne peut ajouter. Plus rien à demander aux clients.

C'est le mécanisme **Application Default Credentials (ADC)** de Google.

### Étape unique : se connecter

```bash
gcloud auth application-default login \
  --scopes=https://www.googleapis.com/auth/webmasters.readonly,https://www.googleapis.com/auth/analytics.readonly,https://www.googleapis.com/auth/cloud-platform
```

- Une fenêtre de consentement Google s'ouvre → connectez-vous avec le compte qui
  a accès à vos clients (ex. `remi.oravec@seo-monkey.fr`).
- Les 3 scopes couvrent : Search Console (lecture), Analytics (lecture), et le
  projet de quota.
- Un fichier est créé ici :
  `~/.config/gcloud/application_default_credentials.json`

### Brancher les serveurs

`.mcp.json.example` (Option A) pointe déjà les deux serveurs sur ce fichier ADC.
Il suffit de `cp .mcp.json.example .mcp.json`, puis de relancer Claude Code.

`GOOGLE_PROJECT_ID` est fixé à `agence-seo-mcp` (votre projet, APIs déjà activées).

### Vérifier

Dans Claude Code :
> « Liste les propriétés GA4 accessibles » → outil `get_account_summaries`
> « Donne les top requêtes GSC des 28 derniers jours pour `<un de vos sites>` »

## Cas particulier GSC

Le serveur `mcp-server-gsc` est documenté pour compte de service, mais la
librairie Google accepte aussi un fichier ADC « authorized_user ». **Testez
l'Option A d'abord.** S'il refuse les identifiants utilisateur, deux solutions :

1. **Serveur GSC nativement OAuth** : il existe des MCP Search Console conçus
   pour OAuth (client ID « Desktop app », consentement navigateur, fichier
   `client_secrets.json`). On en branche un à la place de `mcp-server-gsc` —
   mêmes données, auth utilisateur native.
   Réf. : <https://suganthan.com/blog/google-search-console-mcp-server/>
2. **Documentation Google** sur l'auth MCP / ADC :
   <https://docs.cloud.google.com/mcp/authenticate-mcp> et
   <https://docs.cloud.google.com/sdk/gcloud/reference/auth/application-default/login>

## Et les propriétés où vous n'avez AUCUN accès ?

Si pour un client vous n'êtes ni admin, ni utilisateur, ni propriétaire (ni en
GSC ni en GA4), aucune méthode technique ne contourne ça : le propriétaire doit
vous accorder un accès en lecture (à votre compte Google, pas au compte de
service). C'est la seule étape qui dépend réellement d'un tiers.
