# Documents — le cœur du cockpit

4 dossiers, 4 étapes du travail SEO. On lit les data (via **MCP GSC**), puis on
remplit ces dossiers, dans cet ordre :

| # | Dossier | À quoi ça sert | Question à laquelle il répond |
|---|---------|----------------|-------------------------------|
| 1 | `01-strategie/` | La vision SEO du client | « Où va-t-on, et pourquoi ? » |
| 2 | `02-roadmaps/` | Le découpage dans le temps | « Quoi, dans quel ordre, quand ? » |
| 3 | `03-controle-qualite/` | Les vérifications | « Est-ce bien fait ? » |
| 4 | `04-plan-action/` | Les tâches concrètes | « Qui fait quoi cette semaine ? » |

## Comment on s'en sert ensemble

- Tu me dis *« stratégie pour le client X »* → je tire les data GSC, je croise avec
  le registre `clients/clients.json`, et je remplis le modèle du dossier concerné.
- Chaque dossier contient un fichier `_modele-*.md` : la trame type. Pour un
  nouveau client, on copie le modèle et on le renomme (ex. `clientX-2026-T3.md`).
- Les modèles sont versionnés (`_modele-*`), les livrables clients restent locaux
  si tu préfères ne pas les pousser.
