---
name: preparation-entretien
description: "Prepare un dossier d entretien complet dans Google Drive quand une candidature passe en 1er Entretien ou Entretien metier dans le Notion de You. S appuie sur le CV Drive, la fiche de poste et des recherches web. Declencher des qu une candidature change de statut vers un entretien."
---

# Préparation Entretien — Agent autonome

Produit un dossier de préparation d'entretien complet dans Google Drive, adapté au profil de l'intervieweur, à partir des données Notion et du CV You.

## Pipeline obligatoire — ne pas sauter d'étapes

```
Étape 1 : Lecture Notion (déclencheur + collecte données)
    ↓
Étape 2 : Collecte des sources (CV Drive + fiche de poste + web search)
    ↓
Étape 3 : Détermination du profil intervieweur
    ↓
Étape 4 : Génération des 8 modules
    ↓
Étape 5 : Création du Google Doc formaté (python-docx + Chrome MCP)
    ↓
Étape 6 : Ajout du lien Google Doc en commentaire dans la ligne Notion
```

Ne jamais générer le dossier sans avoir collecté CV + fiche de poste + au moins 3 résultats web sur l'entreprise.

---

## Étape 1 — Lecture Notion

Source : YOUR_NOTION_DATABASE_URL

Déclencheur : colonne "État du process" contient l'une de ces valeurs :
- 1er Entretien → profil intervieweur = RH (certain)
- Entretien métier → profil intervieweur = CEO ou Pair (à déterminer étape 3)

Colonnes à lire obligatoirement :
- Nom de l'entreprise
- Intitulé du poste
- URL (= lien vers la fiche de poste)
- Nom(s) de l'intervieweur(s) si renseigné(s)
- Date de l'entretien si renseignée

SI une colonne obligatoire est vide → stopper et notifier You avant de continuer.

---

## Étape 2 — Collecte des sources

### 2A — CV You

PRIORITÉ 1 : Chercher un Google Doc dans Drive dont le nom contient [Nom entreprise] (insensible à la casse).
- Utiliser list_recent_files pour lister les fichiers récents et rechercher dans les titres ET dans contentSnippet
- Les CV de You sont des Google Docs (pas des .docx) — chercher mimeType application/vnd.google-apps.document
- SI trouvé → utiliser ce fichier (lire via read_file_content avec l'ID du Google Doc)
- SI non trouvé → utiliser le fichier le plus récent dont le titre contient "CV" comme fallback ET noter en haut du Google Doc : ⚠️ "Aucun CV spécifique trouvé pour [Nom entreprise] — CV générique utilisé"

Extraire du CV : parcours chronologique, titres de postes, réalisations chiffrées, compétences clés, stack outils mentionnés.

### 2B — Fiche de poste

ÉTAPE 1 — Tenter web_fetch sur l'URL Notion avec timeout 20s.

SI web_fetch réussit → extraire :
- Titre exact du poste (peut différer de l'intitulé Notion)
- Missions principales et responsabilités
- Compétences et critères requis (années d'expérience, profils recherchés)
- Taille et composition de l'équipe
- Nom et titre du manager direct (reporting)
- Fourchette salariale si affichée
- Process de recrutement détaillé (étapes nommées)
- Stack outils mentionnés
- Valeurs et culture affichées

SI web_fetch échoue ou timeout → basculer immédiatement sur Chrome MCP :
- mcp__Claude_in_Chrome__navigate sur l'URL
- mcp__Claude_in_Chrome__get_page_text pour récupérer le texte
- Extraire les mêmes éléments

SI les deux échouent → noter dans le doc : ⚠️ "Fiche de poste inaccessible — contenu inféré depuis les données Notion et les recherches web" et continuer avec les données disponibles.

### 2C — Recherche web (minimum 5 recherches)

1. "[Nom entreprise] site officiel présentation"
2. "[Nom entreprise] LinkedIn actualités 2025 2026"
3. "[Nom entreprise] concurrents positionnement marché"
4. "[Nom entreprise] levée de fonds clients références"
5. "[Nom(s) intervieweur(s)] LinkedIn parcours" ← si noms disponibles

---

## Étape 3 — Détermination du profil intervieweur

SI "État du process" = "1er Entretien"
    → profil = RH
    → passer directement à l'Étape 4

SI "État du process" = "Entretien métier"
    → lire le titre LinkedIn de l'intervieweur (résultats recherche 2C.5)
    → SI titre contient : CEO / Président / Fondateur / DG / Managing Director / Co-founder
        → profil = CEO
    → SINON
        → profil = PAIR MÉTIER
    → SI titre non trouvé → profil = PAIR MÉTIER par défaut

---

## Étape 4 — Génération des 8 modules

### Ce que j'ai compris de l'entreprise  Elevator pitch entreprise (2 lignes max) 

 Avant le Module 1, inclure un paragraphe de 2 lignes résumant ce qui a été compris de l'entreprise : activité, modèle, marché cible, stade de développement. Formulation synthétique type "X fait Y pour Z, dans un contexte de W."

### Module 1 — Fiche entreprise

- Activité principale + modèle économique
- Taille (effectifs, CA si disponible)
- Positionnement & proposition de valeur
- Actualité récente (levée de fonds, lancement produit, partenariats, recrutements massifs)
- Culture / valeurs affichées (utiliser les valeurs exactes de la fiche de poste si disponibles)
- Pourquoi ce poste attire You → TOUJOURS générer une proposition (voir règle ci-dessous)

RÈGLE "Pourquoi ce poste attire You" :
Ne jamais laisser cette section vide. Toujours générer une proposition en 3 paragraphes :
1. Lien entre le parcours de You et la cible / le marché de l'entreprise (ancré sur le CV)
2. Lien entre ce que You sait faire et l'enjeu précis du poste (ancré sur la fiche de poste)
3. Lien avec la mission ou le sujet de l'entreprise (sincère, pas générique)
Terminer par : "→ [À adapter par You avant l'entretien]"

---

### Module 2 — Marché / ICPs / Personas

- Taille et dynamique du marché (croissance, tendances, maturité)
- ICPs principaux (profil client idéal : secteur, taille d'entreprise, rôle décisionnaire)
- Personas acheteurs / utilisateurs
- Messaging actuel de l'entreprise (slogan, angle de communication, tonalité)
- Opportunités et tensions du marché (ce qui pousse les clients à chercher une solution)

---

### Module 3 — Benchmark concurrentiel

- Top 3-5 concurrents directs : nom, positionnement, forces, faiblesses
- Concurrents indirects / alternatives au produit
- Avantage différenciant de l'entreprise vs concurrents
- Angles marketing que les concurrents n'exploitent pas (opportunités de différenciation)

---

### Module 4 — Diagnostic marketing rapide

Analyser chaque axe à partir des informations publiques disponibles. Conclure par 2-3 quick wins identifiés que You pourrait proposer en entretien.

- Site web & SEO : messaging, UX, visibilité organique, lacunes de contenu
- Contenu & Éditorial : blog, newsletter, livres blancs, podcast, fréquence
- Réseaux sociaux : canaux actifs, ton, engagement, personal branding des fondateurs
- Lead generation : webinars, events, paid ads, outbound visible
- Alignement marketing-sales : signes d'une approche ABM, RevOps visible

Quick wins identifiés : 2-3 actions concrètes, formulées en opportunité (pas en critique)

---

### Module 5 — Plan 90 jours

Jours 1-30 — Écoute & Audit :
- Rencontres individuelles (équipe marketing, sales, produit, manager direct, quelques clients si accessible)
- Audit de l'existant : stack, analytics, contenus, performances pipeline
- Livrable : "État des lieux" partagé avec le manager

Jours 31-60 — Co-construction :
- Co-construction du plan annuel avec le manager et les équipes sales/RevOps
- Priorisation des canaux selon coût d'acquisition cible
- Calibrage KPIs : MQL, SQL, CPL, taux de conversion, revenue attribuable
- Lancement des 2-3 quick wins identifiés

Jours 61-90 — Premiers résultats :
- Exécution des premières campagnes avec reporting régulier
- Premier bilan présenté au manager / CODIR avec projections
- Structuration des rituels marketing-sales (SLA, lead handoff, feedback loops)

Adapter les livrables et la temporalité selon la taille de l'entreprise et le périmètre du poste.

---

### Module 6 — Fiche intervieweur(s)

TOUJOURS inclure, même si peu d'informations disponibles :

Pour chaque intervieweur identifié :
- Nom + titre + ancienneté dans l'entreprise
- Parcours avant (postes clés, secteurs, entreprises notables)
- Centres d'intérêt professionnels visibles (posts LinkedIn, prises de parole)
- Angle probable de ses questions selon son profil
- 1-2 points de connexion potentiels avec le parcours de You

TOUJOURS inclure le process de recrutement complet de l'entreprise :
- Extraire les étapes nommées de la fiche de poste (ex : "Phone interview → Background Interview → Case study → Values fit")
- Si non disponible dans la fiche : noter les étapes standards et indiquer qu'elles sont à confirmer
- Préciser à quelle étape correspond cet entretien
- Préciser ce que vise cette étape spécifique (screening, évaluation technique, culture fit, etc.)

---

### Module 7 — Pitch de présentation

Règles absolues :
- Toujours lire le CV avant de rédiger — le pitch s'ancre sur les vraies réalisations de You
- Toujours intégrer les mots-clés exacts de la fiche de poste
- Réalisations chiffrées obligatoires dans la version 3 min (au moins 2)
- Ne jamais annoter "version RH" ou "version CEO" dans le document final

Structure commune :
1. Accroche — Qui je suis en une phrase (jamais "Je m'appelle You et j'ai X ans d'expérience")
2. Fil rouge — La logique du parcours
3. Réalisations clés — Ce que j'ai construit / produit / transformé
4. Pourquoi ici — Ce qui attire dans CE poste et CETTE entreprise
5. Transition — Ouverture vers l'échange

Version 1 min (150-180 mots) :
- Accroche forte
- Fil rouge en 2-3 phrases max
- 1 réalisation marquante chiffrée ancrée sur la fiche de poste
- Pourquoi ce poste / cette entreprise en 1 phrase
- Transition vers l'échange

Version 3 min (400-450 mots) :
- Accroche forte
- Fil rouge (progression logique, pas une liste de postes)
- 2-3 réalisations chiffrées ancrées sur les mots-clés de la fiche de poste
- Mention d'un enjeu réel de l'entreprise que You peut adresser
- Projection : ce que You veut construire dans ce rôle
- Transition vers l'échange

Adaptation profil RH :
- Focus : cohérence du parcours, motivation sincère, soft skills, stabilité, culture fit
- Éviter : jargon trop technique, réalisations trop abstraites
- Formuler : "Ce qui m'a toujours guidée..." / "Ce qui m'attire ici, c'est..."

Adaptation profil CEO :
- Focus : impact business chiffré, vision stratégie + exécution, levier de croissance
- Éviter : détails opérationnels sans lien avec CA, leads ou pipeline
- Formuler : "J'ai généré X de pipeline..." / "Chez [Entreprise], je vois une opportunité de..."

Adaptation profil Pair métier :
- Focus : expertise concrète, méthodes, outils réels, collaboration inter-équipes
- Éviter : rester trop stratégique/conceptuel, paraître arrogant
- Formuler : "Concrètement, voici comment j'ai abordé..." / "J'utilise [outil] parce que..."

---

### Module 8 — Questions

PARTIE A — Questions à anticiper avec réponses préparées

5-7 questions probables selon le profil. Pour chaque question : réponse ancrée sur le CV de You ET les mots-clés de la fiche de poste. Utiliser le format STAR (Situation / Tâche / Action / Résultat) pour les questions comportementales.

Profil RH — Questions probables :
1. Parlez-moi de vous → Pitch 1 min
2. Pourquoi ce poste / cette entreprise ? → Ancrer sur l'actualité et la fiche de poste
3. Pourquoi quittez-vous votre poste actuel ? → Positif, orienté projection, jamais de critique
4. Quelle est votre plus grande réalisation marketing ? → STAR avec impact chiffré
5. Comment gérez-vous une équipe ? → Exemple concret, style de management
6. Qu'est-ce qui vous différencie des autres candidats ? → 3 éléments spécifiques au poste
7. Où vous voyez-vous dans 3 ans ? → Ancré sur l'ambition de l'entreprise et le poste
8. Quelles sont vos prétentions salariales ? → TOUJOURS inclure la fourchette affichée dans la fiche de poste comme ancre. Si non disponible, indiquer "fourchette à compléter par You selon marché"

Profil CEO — Questions probables :
1. Quel est votre plus grand succès marketing ? → STAR avec impact business chiffré
2. Comment vous y prendriez-vous les 3 premiers mois ? → Plan 90 jours synthétisé
3. Qu'avez-vous vu dans notre marketing qui pourrait être amélioré ? → Diagnostic Module 4, formulé en opportunité
4. Avez-vous déjà construit un département marketing from scratch ? → STAR ou exemple de structuration
5. Comment vous alignez-vous avec les équipes sales ? → Exemples concrets (SLA, lead scoring, ABM)
6. Quelle serait votre première priorité ici ? → Ancré sur fiche de poste + diagnostic
7. Quel budget avez-vous géré ? → Chiffres réels du CV, sans gonfler

Profil Pair métier — Questions probables :
1. Quels outils marketing utilisez-vous au quotidien ? → Outils du CV + ceux de la fiche de poste
2. Comment organisez-vous votre roadmap marketing ? → Méthode concrète (sprints, OKRs...)
3. Donnez-moi un exemple de campagne pilotée de A à Z → STAR complet
4. Comment gérez-vous les désaccords avec les autres équipes ? → Exemple de friction résolue
5. Quelle est votre approche de la mesure de performance ? → KPIs, outils de reporting, fréquence de review
6. Comment vous tenez-vous à jour dans votre domaine ? → Sources, veille, communautés, formations
7. Qu'est-ce que vous ne savez pas faire et aimeriez apprendre ? → 1 vrai angle de développement

PARTIE B — Questions à poser à l'intervieweur

Profil RH :
1. Quelle est la principale priorité pour ce poste dans les 6 premiers mois ?
2. Qu'est-ce qui différencie les personnes qui s'épanouissent ici de celles qui n'y trouvent pas leur place ?
3. Comment fonctionne la collaboration entre marketing et les équipes sales / RevOps aujourd'hui ?
4. Comment est composée l'équipe marketing et quelle est la vision d'organisation à 12-18 mois ?
5. Quelles sont les prochaines étapes du process et les délais envisagés ?

Profil CEO :
1. Quelle est la priorité numéro 1 pour le marketing dans les 6 prochains mois ?
2. Comment le marketing est-il perçu dans l'entreprise — centre de coût ou levier de croissance ?
3. Qu'est-ce qui a manqué jusqu'ici dans la fonction marketing ?
4. Comment mesurez-vous le succès de ce recrutement à 6 mois ?
5. Quelle est votre vision à 2-3 ans pour l'entreprise ?

Profil Pair métier :
1. Comment est organisée l'équipe marketing aujourd'hui et comment ce poste s'y intègre-t-il ?
2. Quels sont les outils et process déjà en place que je reprendrais ?
3. Quels sont les chantiers les plus urgents selon vous ?
4. Comment se passe la collaboration entre marketing et les autres équipes (sales, produit, CS) ?
5. Qu'est-ce qui vous plaît le plus dans votre façon de travailler ensemble ?

---

## Étape 5 — Création du Google Doc formaté

⚠️ NE PAS utiliser create_file avec contentMimeType "text/plain" ou "text/html" : aucun de ces formats ne produit des titres colorés dans Google Docs.
- text/plain → converti en Google Doc MAIS sans mise en forme (tout le texte est identique)
- text/html → NON converti (reste un fichier HTML brut dans Drive)

**Méthode correcte : python-docx + upload Chrome MCP + conversion Google Docs**

### 5A — Générer le .docx avec python-docx

```python
pip install python-docx --break-system-packages -q

from docx import Document
from docx.shared import RGBColor

doc = Document()

# Appliquer les couleurs aux styles de titres
doc.styles['Heading 1'].font.color.rgb = RGBColor(0x21, 0x21, 0x21)  # gris foncé
doc.styles['Heading 2'].font.color.rgb = RGBColor(0x15, 0x65, 0xC0)  # bleu
doc.styles['Heading 3'].font.color.rgb = RGBColor(0x00, 0x69, 0x5C)  # teal

# Pour chaque titre ajouté, appliquer aussi la couleur sur le run :
p = doc.add_heading('MODULE 1 — FICHE ENTREPRISE', level=2)
p.runs[0].font.color.rgb = RGBColor(0x15, 0x65, 0xC0)

p = doc.add_heading('Activité & modèle économique', level=3)
p.runs[0].font.color.rgb = RGBColor(0x00, 0x69, 0x5C)
```

Palette de couleurs standardisée :
- H1 (titre principal) : RGBColor(0x21, 0x21, 0x21) — #212121 gris anthracite
- H2 (modules) : RGBColor(0x15, 0x65, 0xC0) — #1565C0 bleu
- H3 (sous-sections) : RGBColor(0x00, 0x69, 0x5C) — #00695C teal

Sauvegarder dans le dossier workspace de You :
```python
doc.save('/tmp/[NomEntreprise]_Entretien.docx')
```

### 5B — Upload via Chrome MCP

```
1. navigate → https://drive.google.com/drive/folders/YOUR_DRIVE_FOLDER_ID
2. computer left_click sur "Nouveau" (bouton en haut à gauche)
3. computer left_click sur "Importer un fichier"
4. find("file upload input element") → récupérer ref
5. file_upload paths=["/Users/mouni/Documents/Claude/Projects/Péparation entretien/[NomEntreprise]_Entretien.docx"] ref=<ref>
6. Attendre 3 secondes → vérifier screenshot (toast "1 importation terminée")
```

### 5C — Conversion en Google Doc natif

```
7. double_click sur le fichier .docx dans Drive → s'ouvre dans un nouvel onglet Google Docs
8. Dans l'onglet Google Docs : computer left_click sur "Fichier" (menu)
9. computer left_click sur "Enregistrer au format Google Docs"
10. Attendre 3-4 secondes → un NOUVEL onglet s'ouvre avec le Google Doc natif (sans badge DOCX)
11. Récupérer l'ID du Google Doc depuis l'URL du nouvel onglet :
    URL : https://docs.google.com/document/d/[ID]/edit
    → L'ID est la chaîne entre /d/ et /edit
```

Le Google Doc natif aura automatiquement :
- Un sommaire interactif dans la barre latérale gauche (généré depuis les styles Heading 2/3)
- Les couleurs de titres préservées depuis le .docx

Nom du fichier final dans Drive : [NomEntreprise]_1er Entretien ou [NomEntreprise]_Entretien métier
Dossier cible : "Préparation entretiens" — ID : YOUR_DRIVE_FOLDER_ID

---

## Étape 6 — Mise à jour Notion

- Utiliser l'ID du Google Doc natif (étape 5C) pour construire l'URL : https://docs.google.com/document/d/[ID]/edit
- Ajouter cette URL en commentaire sur la page Notion de la candidature
- Ne pas modifier les autres colonnes

---

## Règles globales

Toujours lire le CV avant de rédiger le pitch. Le pitch s'ancre sur les vraies réalisations de You, pas sur des formules génériques.

Toujours fetcher la fiche de poste. Si web_fetch échoue → basculer sur Chrome MCP immédiatement sans attendre.

Toujours extraire de la fiche de poste : titre exact, fourchette salariale, taille d'équipe, nom du manager direct, process de recrutement complet.

Toujours générer une proposition pour "Pourquoi ce poste attire You" — ne jamais laisser vide.

Adapter le registre au profil sans jamais le mentionner explicitement dans le document. Le dossier est personnel à You.

Si web search ne trouve pas d'infos sur l'entreprise → indiquer clairement : "Informations limitées disponibles publiquement — privilégier les questions en entretien."

Si l'intervieweur n'est pas identifiable → compléter la fiche intervieweur avec ce qui est connu, laisser les champs inconnus vides plutôt qu'inventer.

Longueur cible du Google Doc : 4 à 6 pages. Concis et opérationnel — You doit pouvoir le relire en 20 min la veille.