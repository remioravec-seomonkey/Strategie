# Guide d'installation — Accès Google

> ⚠️ **Méthode recommandée : OAuth avec votre propre compte** — voir
> **[`AUTH.md`](AUTH.md)**. Dans un contexte d'agence, le compte de service se
> heurte à deux murs (Search Console refuse les comptes de service dans son UI ;
> vous n'êtes pas admin des propriétés clients). OAuth/ADC utilise votre accès
> existant et débloque tout sans rien demander aux clients.
>
> Le projet Google Cloud (`agence-seo-mcp`) et les API sont **déjà** créés/activés.
> Pour l'auth recommandée, passez directement à `AUTH.md`. Le guide ci-dessous
> documente l'alternative « compte de service ».

---

## 1. Créer le projet et activer les API

1. Allez sur <https://console.cloud.google.com/> et créez (ou choisissez) un projet.
   Notez son **ID de projet** (ex. `agence-seo-470512`).
2. Activez les deux API :
   - **Google Search Console API**
   - **Google Analytics Data API** (et **Google Analytics Admin API**)
   Menu *APIs & Services → Library*, cherchez chaque API, cliquez *Enable*.

## 2. Créer le compte de service

1. *APIs & Services → Credentials → Create credentials → Service account*.
2. Nommez-le (ex. `mcp-seo-reader`), rôle : aucun rôle projet nécessaire (l'accès
   se fait au niveau de chaque propriété).
3. Une fois créé, ouvrez-le → onglet **Keys → Add key → Create new key → JSON**.
4. Téléchargez le fichier et placez-le ici :
   ```
   credentials/service-account.json
   ```
   (Ce chemin est déjà ignoré par git — il ne sera jamais poussé.)
5. Notez l'**email** du compte de service, du type
   `mcp-seo-reader@VOTRE-PROJET.iam.gserviceaccount.com`.

## 3. Autoriser le compte de service chez chaque client

Pour **chaque** client, répétez :

### Search Console
1. <https://search.google.com/search-console> → sélectionnez la propriété.
2. *Paramètres → Utilisateurs et autorisations → Ajouter un utilisateur*.
3. Collez l'email du compte de service, autorisation **« Lecture complète »**.

### Google Analytics 4
1. <https://analytics.google.com/> → *Admin* → propriété concernée.
2. *Gestion des accès à la propriété → +  → Ajouter des utilisateurs*.
3. Collez l'email du compte de service, rôle **« Lecteur »** (Viewer).
4. Notez l'**ID de propriété GA4** (Admin → *Paramètres de la propriété*,
   nombre à ~9 chiffres, ex. `123456789`).

## 4. Renseigner le registre clients

Copiez l'exemple et remplissez-le :
```
cp clients/clients.example.json clients/clients.json
```
Pour chaque client : `gsc_site_url` (format `sc-domain:exemple.com` pour une
propriété de domaine, ou `https://www.exemple.com/` pour une propriété d'URL) et
`ga4_property_id` (l'ID numérique noté à l'étape 3).

## 5. Activer les serveurs MCP

```
cp .mcp.json.example .mcp.json
```
Ouvrez `.mcp.json`, remplacez `REMPLACER_PAR_VOTRE_PROJECT_ID` par l'ID de votre
projet Google Cloud, puis (re)lancez Claude Code dans ce dossier.

### Prérequis machine
- **Node.js 18+** (pour `mcp-server-gsc` via `npx`)
- **Python 3.10+** et **pipx** (pour `analytics-mcp`)
  ```
  python3 -m pip install --user pipx && python3 -m pipx ensurepath
  ```

## 6. Vérifier

Dans Claude Code, demandez par exemple :
> « Liste les propriétés GA4 accessibles » (outil `get_account_summaries`)

Si la liste de vos clients remonte, l'accès fonctionne. ✅

---

### Dépannage rapide
- **403 / PERMISSION_DENIED** : le compte de service n'a pas (encore) été ajouté à
  cette propriété, ou l'API n'est pas activée.
- **GA4 vide alors que GSC marche** : vérifiez l'`ga4_property_id` (ID numérique,
  pas l'ID de flux/measurement `G-XXXX`).
- **`pipx`/`npx` introuvable** : installez les prérequis machine ci-dessus.
