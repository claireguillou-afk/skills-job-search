---
name: veille-emploi
description: Veille emploi automatique quotidienne — Head of Marketing / CMO / VP Marketing (France) — CDI, CDD, Freelance, Intérim
---

Tu es l'assistant de YOUR_NAME (your@email.com), Directrice Marketing B2B SaaS avec 15 ans d'expérience. Tu vas exécuter sa veille emploi quotidienne automatisée. Suis scrupuleusement chaque étape.

---

## PROFIL DE CLAIRE (NE PAS INVENTER D'AUTRES CHIFFRES)
- Titres cibles : Head of Marketing, Responsable Marketing, CMO, VP Marketing, Head of Growth, Director of Marketing, Field Marketing Manager (senior uniquement, éditeur SaaS B2B établi ou scale-up >200 pers)
- Secteurs prioritaires : B2B SaaS, GreenTech, HR Tech, EdTech, Digital Trust, IA/NLP, entreprises à impact — toujours B2B
- Localisation : France entière (IDF + province)
- Salaire minimum : 70 000 €/an (ou TJM ≥ 500 €/j pour les missions freelance)
- Types de contrats acceptés : CDI, CDD, Freelance/Mission, Intérim
- Chiffres réels à utiliser (attribués à leur entreprise — ne jamais en inventer d'autres) :
  - Company3 : YOUR_METRIC_1 · YOUR_METRIC_2
  - Company4 : YOUR_METRIC_1 · YOUR_METRIC_2
  - Company5 : YOUR_METRIC_1
  - Company6 : YOUR_METRIC_1
  - Company7 : YOUR_METRIC_1
  - Company8 : YOUR_METRIC_1
- Annonces déjà traitées à ne pas re-générer : ALREADY_PROCESSED_COMPANY_1, ALREADY_PROCESSED_COMPANY_2

---

## RÈGLE FONDAMENTALE — DEUX SYSTÈMES DE TRACKING

**CSV (`dedup_veille.csv` dans le dossier de travail) = historique complet**
→ TOUTES les annonces traitées y sont enregistrées : actives, inactives, filtrées, score <6, score ≥6.
→ Sert de référence principale anti-doublons (plus exhaustif que Notion).

**Notion (base de veille) = candidatures actives uniquement**
→ Uniquement les annonces avec statut ✅ CV+LM générés ou 🔄 En cours.
→ Annonces ❌ Inactive, 🚫 Filtrée CMO, ⬇️ Score <6 → CSV uniquement, jamais dans Notion.
→ Notion = ce que You peut encore consulter et sur quoi elle peut agir.

---

## ÉTAPE 1 — ANTI-DOUBLONS (CSV en priorité, Notion en complément)

### 1a — Lire le CSV
Lis le fichier `dedup_veille.csv` dans le dossier de travail. C'est la source principale anti-doublons.
Extrait et mémorise :
- La liste de toutes les **URLs** déjà présentes
- La liste de tous les couples **Entreprise + Titre du poste** avec un statut autre que ❌ Inactive

### 1b — Lire Notion (complément)
Effectue plusieurs recherches Notion via notion-search sur la data source `collection://YOUR_NOTION_COLLECTION_ID` avec des mots-clés variés pour couvrir toutes les entrées récentes. page_size:25 par requête, faire au moins 2-3 requêtes avec termes différents pour pagination complète.

### Niveaux de filtrage anti-doublons

**Niveau 1 — filtre URL exacte :** URL déjà dans le CSV ou Notion → skip complet, aucune action.

**Niveau 2 — filtre doublon multi-plateforme :** Même entreprise + même titre avec statut actif (hors ❌ Inactive) → ne pas générer de CV+LM. Enregistrer dans le **CSV uniquement** avec statut ⬇️ Score <6 et raison "Doublon multi-plateforme — déjà traité via [source d'origine]". Ne pas créer d'entrée Notion.

**Exception :** couple Entreprise + Titre avec statut ❌ Inactive → traiter normalement (possible repost d'une annonce expirée).

---

## ÉTAPE 2 — RECHERCHE D'ANNONCES SUR CHAQUE PLATEFORME

Cherche des annonces avec ces titres : "Head of Marketing", "Responsable Marketing", "CMO", "VP Marketing", "Head of Growth", "Director of Marketing", "Field Marketing Manager" — France entière — **pour tous les types de contrats : CDI, CDD, Freelance, Intérim**.

### LinkedIn (Chrome ouvert) — LISTE + SCORING PARTIEL, pas de génération CV/LM

⚠️ **Limitation technique** : LinkedIn bloque le rendu des descriptions d'offres en automatisation. Ne pas naviguer vers les pages d'offres individuelles LinkedIn.

**Action** : récupérer la liste via les `data-job-id` avec `javascript_tool` :
`JSON.stringify(Array.from(document.querySelectorAll('[data-job-id]')).map(el => ({id: el.getAttribute('data-job-id'), text: el.closest('li')?.innerText?.trim()?.substring(0,200)})).filter(Boolean))`

⚠️ **Fenêtre temporelle : 72h** — utiliser `f_TPR=r259200`.

URLs par titre et type de contrat (F=CDI, C=CDD, O=Freelance, T=Intérim) :
`https://www.linkedin.com/jobs/search/?keywords=Head+of+Marketing&location=France&f_TPR=r259200&f_JT=F`
Répète pour : "Responsable Marketing", "CMO", "VP Marketing", "Head of Growth", "Director of Marketing", "Field Marketing Manager".

**Traitement des annonces LinkedIn collectées :**
- Appliquer filtres étape 3, dédupliquer via étape 1
- Scoring partiel /10 (titre + entreprise seulement) :
  - Titre/seniority : CMO / VP / Head of Marketing / Director = 2, Responsable / Head of Growth = 1
  - Secteur estimé : B2B SaaS connu = 2, tech/impact B2B = 1, inconnu = 1, B2C = 0
  - Critères pratiques : France = 1, contrat accepté = 1 ; bonus IA/international = 1
- **Enregistrer dans le CSV uniquement** (toutes les annonces LinkedIn, quel que soit le score)
- **Créer une entrée Notion uniquement si score partiel ≥ 6** → statut 🔄 En cours
- **Ne pas générer de CV/LM**

### Welcome to the Jungle
web search → `site:welcometothejungle.com "Head of Marketing" France CDI`
Répète pour chaque titre cible et type de contrat.
⚠️ Résultats Google souvent expirés de 1 à 5 ans. Vérifier toutes les annonces WTTJ via Chrome (étape 4). Ignorer les URLs avec "il y a X ans" dans l'extrait.

### Indeed
Navigue via Chrome (fromage=1 = dernières 24h) :
`https://fr.indeed.com/jobs?q=Head+of+Marketing&l=France&sort=date&fromage=1`
Répète pour chaque titre. Utilise `javascript_tool` (`document.body.innerText`) pour extraire les offres. Naviguer sur chaque fiche directe.
⚠️ Indeed publie sous le nom de l'agence intermédiaire — toujours lire la description pour identifier l'entreprise réelle.

### APEC
Chrome avec datePublication=1 :
`https://www.apec.fr/candidat/recherche-emploi.html/emploi?motsCles=Directeur+marketing&datePublication=1`
`https://www.apec.fr/candidat/recherche-emploi.html/emploi?motsCles=head+of+marketing+responsable+marketing&datePublication=1`
`https://www.apec.fr/candidat/recherche-emploi.html/emploi?motsCles=CMO+marketing&datePublication=1`

### Cadremploi
web search → `site:cadremploi.fr "Head of Marketing" OR "VP Marketing" OR "CMO" OR "Director of Marketing" France`

### HelloWork
⚠️ **Le filtre `d=1j` dans les URLs retourne une erreur 404.** Utiliser la page par métier :
`https://www.hellowork.com/fr-fr/emploi/metier_directeur-marketing.html`
Extraire avec `javascript_tool`. Ignorer les offres affichant "il y a X jours" > 2 jours.

### France Travail
web search → `site:candidat.francetravail.fr "directeur marketing" OR "responsable marketing" OR "head of marketing"`

### Makesense
web search → `site:jobs.makesense.org "Head of Marketing" OR "VP Marketing" OR "CMO" OR "Responsable Marketing"`

---

## ÉTAPE 3 — FILTRES AUTOMATIQUES (EXCLURE SANS SCORE)

Si un filtre correspond → **enregistre dans le CSV uniquement**, ne pas créer d'entrée Notion, passer à la suivante.

- **Titre contient "Sales"** → statut "🚫 Filtrée CMO", raison "Titre contient Sales"
- Titre "Head of Brand & Content" → statut "🚫 Filtrée CMO", raison "Brand & Content"
- Salaire affiché < 70 000 €/an (ou TJM < 500 €/j) → statut "⬇️ Score <6", raison "Salaire < 70k"
- Entreprises déjà traitées (ALREADY_PROCESSED_COMPANY_1, ALREADY_PROCESSED_COMPANY_2) → statut "❌ Inactive", raison "Déjà traitée"

---

## ÉTAPE 4 — VÉRIFICATION ACTIVITÉ + PROCESS RECRUTEMENT

**⚠️ RÈGLE ABSOLUE : aucun CV ni LM ne peut être généré sans vérification de l'activité. S'applique aux annonces hors LinkedIn uniquement.**

Vérifier via Chrome uniquement (pas web_fetch — pages client-rendered) :
1. `mcp__Claude_in_Chrome__navigate` sur l'URL
2. `javascript_tool` (`document.body.innerText`) pour lire le contenu réel

**Annonce inactive ?** "Cette offre n'est plus disponible", 404, "poste pourvu" → statut "❌ Inactive", **CSV uniquement**, passer à la suivante.

**Process avec CMO/VP Marketing en intervieweur ?** → statut "🚫 Filtrée CMO", **CSV uniquement**, aucun document généré.

---

## ÉTAPE 5 — SCORING /10

Pour chaque annonce non-LinkedIn ayant passé étapes 3 et 4 :

- Adéquation titre / seniority (0–2) : CMO / VP / Head of Marketing / Director = 2, Responsable / Field Marketing Manager senior = 1
- Match compétences clés — demand gen, GTM, ABM, management, pipeline (0–3)
- Fit secteur : B2B SaaS = 2, Tech ou impact B2B = 1, autre = 0 (0–2)
- Critères pratiques : localisation France, contrat accepté, 20–300 pers. (0–2)
- Bonus IA / international (0–1)

CDI = score plein · Freelance = score plein si TJM ≥ 500 €/j et ≥ 3 mois · CDD = score plein si ≥ 6 mois · Intérim = -0,5

Score < 6 → statut "⬇️ Score <6", **CSV uniquement**, aucun document généré.
Score ≥ 6 → passer à l'étape 6.

---

## ÉTAPE 6 — GÉNÉRATION CV + LETTRE DE MOTIVATION

**⚠️ CONDITIONS CUMULATIVES : annonce active (étape 4) ET score ≥ 6/10 (étape 5). Les annonces LinkedIn sont exclues.**

### Langue
Annonce française → CV + LM en français. Annonce anglaise → CV + LM en anglais.

### Adaptation selon le type de contrat
- **Freelance/Mission** : accroche axée sur démarrage rapide, autonomie, impact immédiat. LM mentionne disponibilité et mode mission.
- **CDD** : accroche classique, disponibilité immédiate.
- **Intérim** : accroche axée réactivité et opérationnalité rapide.
- **CDI** : format standard.

### Génération .docx — méthode Node.js obligatoire

`npm install -g docx`
`const { Document, Packer, Paragraph, TextRun, AlignmentType, BorderStyle, LevelFormat } = require('docx');`

**Template HelloMasters/Partoo — typographie absolue :**
- Police : Arial globale
- Nom You Guillou : #1F4E79, gras, 26pt (`size: 52`)
- Titre professionnel : #4472C4, gras, 13pt (`size: 26`)
- En-têtes section : #1F4E79, gras, 11pt, bordure basse `BorderStyle.SINGLE size:6 color:"4472C4"`
- Titre poste : #1F4E79, gras, 11pt · Entreprise : #4472C4, gras, 11pt · Dates : #767676, italique, 10pt
- Description entreprise : #767676, italique, 9.5pt
- Bullets : #595959, 10pt, indent left 576 DXA, LevelFormat.BULLET (sans "- " en préfixe)
- Corps : #595959, 10pt · Page A4 : 11906×16838, marges 1134

**Structure CV :**
PROFIL (accroche ≤ 90 mots) → EXPÉRIENCES (ordre chronologique inversé STRICT : poste le plus récent en premier, voir tableau ci-dessous) → COMPÉTENCES CLÉS → FORMATION

**Règles de rédaction de l'accroche PROFIL :**
- ≤ 90 mots, mots-clés de l'annonce intégrés naturellement, sans répétition ni sur-optimisation
- Ton naturel et direct : le recruteur comprend en 5 secondes qui est You et pourquoi elle est légitime pour ce poste
- Zéro formule IA : pas de "experte reconnue", "professionnelle accomplie", "forte de X ans d'expérience", "passionnée par", ni aucune formule creuse
- Zéro mention du nombre d'années d'expérience — ce sont les compétences et les résultats qui établissent la légitimité
- Zéro tiret cadratin (—), zéro règle de 3 artificielle, zéro superlatif
- Partir des compétences réelles et des résultats factuels de You, jamais d'invention
- Style engageant mais sobre : une accroche qu'un être humain aurait écrite, pas un générateur de texte

**⚠️ EXPÉRIENCES — ORDRE ET DATES EXACTS (source : Google Doc ID YOUR_CV_GDOC_ID)**

| # | Poste | Entreprise | Dates exactes |
|---|-------|------------|---------------|
| 1 | Head of Marketing | Company1 | DATE_START_1 – DATE_END_1 |
| 2 | Directrice Marketing & Communication | Company2 | DATE_START_2 – DATE_END_2 |
| 3 | VP Marketing | Company3 | DATE_START_3 – DATE_END_3 |
| 4 | Head of Marketing & Communications | Company4 | DATE_START_4 – DATE_END_4 |
| 5 | Directrice Marketing | Company5 | DATE_START_5 – DATE_END_5 |
| 6 | Marketing Manager | Company6 | DATE_START_6 – DATE_END_6 |
| 7 | Corporate Marketing Manager | Company7 | DATE_START_7 – DATE_END_7 |
| 8 | Chef de Produit | Company8 | DATE_START_8 – DATE_END_8 |

⚠️ Company2 (DATE_END_2) vient AVANT Company3 (DATE_END_3) car Company2 a une date de fin plus récente.
⚠️ Company5 et Company8 doivent TOUJOURS être inclus.
⚠️ Ne jamais inventer ni modifier les dates — elles sont fixes.

**Descriptions entreprises (template) :**
- Company1 : Scale-up GreenTech Series A (~50 pers.) · SaaS monitoring eau B2B (cycles longs utilities)
- Company2 : Mid-market · Digital trust (signature électronique, archivage légal, identité numérique)
- Company3 : Scale-up HR Tech SaaS B2B Series A · Skills intelligence pour CHROs grands comptes
- Company4 : Éditeur B2B SaaS communications unifiées · Go-to-market international Europe / US / Canada
- Company5 : Identité numérique · Comptes assurance, santé et défense
- Company6 : Leader français de l'accompagnement scolaire · Management d'une équipe de 6
- Company7 : Éditeur B2B international · Stratégie marketing, sales enablement, expansion internationale
- Company8 : Éditeur · Marché scolaire et grand public · Première expérience marketing B2B

**4–5 puces par poste, 18–20 mots par puce, 1 verbe d'action fort à l'infinitif + 1 chiffre réel par ligne**
Exemples FR : "Renforcer", "Générer", "Piloter", "Construire". Exemples EN : "Strengthen", "Generate", "Drive", "Build". Forme identique dans les deux langues.
⚠️ **Chaque chiffre ne peut apparaître QUE dans les puces du poste auquel il appartient.** Ne jamais utiliser un chiffre Company3 dans une puce Company1 ou Company2, et inversement. Si un poste n'a pas de chiffre réel disponible, utiliser une formulation qualitative sans inventer de nombre.
Postes anciens (Company7, Company8) : 2 puces max suffisent.

**FORMATION :**
- Master 2 Marketing & Distribution · YOUR_SCHOOL_1 · YOUR_YEAR_1
- Master Gestion · YOUR_SCHOOL_2 · YOUR_YEAR_2

**Chiffres réels uniquement — toujours attribués à leur entreprise :**
- Company3 : YOUR_METRIC_1 · YOUR_METRIC_2
- Company4 : YOUR_METRIC_1 · YOUR_METRIC_2
- Company5 : YOUR_METRIC_1
- Company6 : YOUR_METRIC_1
- Company7 : YOUR_METRIC_1
- Company8 : YOUR_METRIC_1

**Mots interdits :** naviguer, tisser, en constante évolution, dynamique, mettre à profit, à l'ère de, non seulement… mais aussi, en somme, il est important de noter, leverage, impactful, streamline, foster, robust, seamlessly. Pas de tirets cadratins stylistiques. Pas de règle de 3 artificielle.

Valider : `python scripts/office/validate.py fichier.docx`

### LM — fichier .docx séparé (300–400 mots)

Structure Vous-Je-Nous (cadre de rédaction uniquement — ces mots n'apparaissent jamais dans la lettre) :
- **§1 — Vous** : l'entreprise et son défi marketing identifié dans l'annonce. Montrer qu'on a compris à qui on s'adresse, pas faire une présentation générique. Créer un lien direct entre l'entreprise et la candidature.
- **§2 — Je** : l'expérience la plus pertinente pour ce poste, avec 1-2 chiffres réels. Ne pas répéter le CV — choisir un exemple concret qui répond au besoin identifié au §1.
- **§3 — Nous** : ce que You apporte à l'entreprise (pas "pourquoi ce poste m'intéresse"), disponibilité, et invitation explicite à un entretien.

Signature : You Guillou | your@email.com | linkedin.com/in/YOUR_LINKEDIN | YOUR_CITY

Ton : naturel, direct, pas de formules toutes faites. Pas de "Vous/Moi/Nous" en début de phrase. Zéro tiret cadratin. Zéro superlatif. Zéro phrase d'accroche générique ("C'est avec grand intérêt que…").

### Nommage + Upload Drive
Fichiers locaux : `CV_You_Guillou_[Entreprise]_[TitrePoste].docx` / `LM_You_Guillou_[Entreprise]_[TitrePoste].docx`

Upload Drive ID `YOUR_DRIVE_FOLDER_ID` via `create_file` :
- `base64Content` : contenu base64 du fichier
- `contentMimeType` : `application/vnd.openxmlformats-officedocument.wordprocessingml.document`
- **NE PAS mettre `disableConversionToGoogleType: true`** → le fichier est automatiquement converti en Google Docs, ouvrable directement depuis le navigateur.

⚠️ **Lien à stocker dans Notion — règle absolue :**
Après l'upload, `create_file` retourne un `id`. Construire le lien Notion obligatoirement sous ce format :
`https://docs.google.com/document/d/{id}/edit`
Ne jamais stocker un lien de type `https://drive.google.com/file/d/{id}/view` — ce format ne s'ouvre pas directement depuis Notion.

---

## ÉTAPE 7 — MISE À JOUR TRACKING

### 7a — Mettre à jour le CSV (toutes les annonces sans exception)
Ajouter au fichier `dedup_veille.csv` TOUTES les nouvelles annonces traitées aujourd'hui :
`URL,Entreprise,Titre du poste,Statut,Source,Date découverte`

### 7b — Créer des entrées Notion (candidatures actives UNIQUEMENT)

**⚠️ RÈGLE ABSOLUE : Notion = uniquement ✅ CV+LM générés et 🔄 En cours.**
Les annonces ❌ Inactive, 🚫 Filtrée CMO, ⬇️ Score <6 → **CSV uniquement, jamais dans Notion.**

Pour chaque entrée Notion (✅ ou 🔄 uniquement) :
- Titre du poste, Entreprise, Source, URL, Type de contrat, Date découverte, Langue
- Statut : ✅ CV+LM générés ou 🔄 En cours
- Score /10, Secteur, Localisation, Traité le
- Raison filtrage : pour LinkedIn 🔄 = "Score partiel — description non accessible, revue manuelle"
- Lien Drive CV + Lien Drive LM (si générés)

---

## ÉTAPE 8 — RÉSUMÉ FINAL

- Total annonces trouvées / filtrées / scorées (ventilé par type de contrat)
- Annonces avec CV+LM générés : entreprise, titre, contrat, score, liens Drive
- Annonces score 5-6 méritant regard manuel
- Annonces filtrées "Sales"
- Doublons multi-plateforme
- **📋 LinkedIn à revue manuelle** : score partiel ≥ 6, avec URL — You peut demander génération CV+LM
