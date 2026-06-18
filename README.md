# Skills Claude — Job Search Automation

Skills personnalisés pour Claude (Cowork), conçus pour automatiser la recherche d'emploi et la préparation d'entretiens.

---

## `preparation-entretien`

**Déclencheur** : une candidature passe au statut "1er Entretien" ou "Entretien métier" dans Notion.

**Ce que ça fait** : produit un dossier de préparation complet dans Google Drive (4–6 pages) en 6 étapes :
1. Lecture Notion (données candidature)
2. Collecte CV Drive + fiche de poste + recherches web
3. Détermination du profil intervieweur (RH / CEO / Pair métier)
4. Génération des 8 modules (fiche entreprise, benchmark, diagnostic marketing, plan 90j, fiche intervieweur, pitch, questions)
5. Création du Google Doc formaté (python-docx + Chrome MCP)
6. Ajout du lien en commentaire Notion

**Prérequis** : Google Drive, Notion, Chrome MCP, accès web search.

---

## `veille-emploi-claire`

**Déclencheur** : à lancer manuellement chaque matin (ou via automatisation quotidienne).

**Ce que ça fait** : veille emploi automatisée sur 7 plateformes (LinkedIn, WTTJ, Indeed, APEC, Cadremploi, HelloWork, France Travail) pour les titres Head of Marketing / CMO / VP Marketing / Head of Growth en France.

Pipeline en 8 étapes :
1. Anti-doublons (CSV + Notion)
2. Recherche multi-plateformes
3. Filtres automatiques
4. Vérification activité des annonces
5. Scoring /10
6. Génération CV + LM (.docx) uploadés sur Drive
7. Mise à jour tracking (CSV + Notion)
8. Résumé final

**Prérequis** : Google Drive, Notion, Chrome MCP, accès web search.

---

## Templates

| Fichier | Description |
|---|---|
| [`dedup_veille_template.csv`](./dedup_veille_template.csv) | Fichier CSV vide pour le suivi anti-doublons |
| [`notion-database-schema.md`](./notion-database-schema.md) | Schéma complet de la base Notion |
| [🔎 Job Search Tracker — Notion](https://app.notion.com/p/11178f3038964ae78fe14812e0f4fd1f?v=62b0cb054ba84d32823f41bce957e4d8) | Base Notion vide prête à l'emploi |

---

## Installation

1. Cloner ce repo
2. Placer les dossiers dans `/mnt/skills/user/` de ton environnement Cowork
3. Les skills apparaissent automatiquement dans la liste des skills disponibles
4. Dupliquer la base Notion template et mettre à jour `YOUR_NOTION_COLLECTION_ID` dans le skill `veille-emploi`
