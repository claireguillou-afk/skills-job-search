# Notion Database Schema — Job Search Tracker

Template for recreating the job search tracking database in Notion.

---

## Database name
`🔎 Job Search Tracker`

---

## Properties

| Property name | Type | Options / Notes |
|---|---|---|
| `Titre du poste` | Title | Main title field |
| `Entreprise` | Text | Company name |
| `URL` | URL | Link to job posting |
| `Source` | Select | See options below |
| `Statut` | Select | See options below |
| `Etat du process` | Select | See options below |
| `Score /10` | Number | 0–10 |
| `Langue` | Select | FR / EN |
| `Type de contrat` | Select | CDI / CDD / Intérim / Freelance |
| `Localisation` | Text | City or remote |
| `Secteur` | Text | Industry sector |
| `Raison filtrage` | Text | Reason for filtering out |
| `Date découverte` | Date | Date the job was found |
| `Date envoi candidature` | Date | Date application was sent |
| `Traité le` | Date | Date the entry was processed |
| `Lien Drive CV` | URL | Google Drive link to tailored CV |
| `Lien Drive LM` | URL | Google Drive link to cover letter |

---

## Select options

### Source
| Label | Color |
|---|---|
| LinkedIn | Blue |
| Welcome to the Jungle | Green |
| Indeed | Orange |
| APEC | Purple |
| Cadremploi | Pink |
| HelloWork | Gray |
| France Travail | Brown |
| Makesense | Red |

### Statut
| Label | Color |
|---|---|
| ✅ CV+LM générés | Green |
| 🔄 En cours | Blue |
| ⬇️ Score <6 | Yellow |
| 🚫 Filtrée | Orange |
| ❌ Inactive | Red |

### Etat du process
| Label | Color |
|---|---|
| Non candidaté | Blue |
| En cours | Brown |
| 1er Entretien | Purple |
| Entretien métier | Default |
| Refusé | Red |
| Pas de réponse | Yellow |
| Pas concerné | Orange |

### Langue
| Label | Color |
|---|---|
| FR | Gray |
| EN | Brown |

### Type de contrat
| Label | Color |
|---|---|
| CDI | Default |
| CDD | Gray |
| Intérim | Red |
| Freelance | Pink |

---

## Recommended default view (Table)

Filters active by default:
- `Source` is one of: LinkedIn, Indeed, Welcome to the Jungle, APEC, Cadremploi
- `Statut` is one of: ✅ CV+LM générés, 🔄 En cours

Visible columns (in order):
`Titre du poste` · `Date découverte` · `Entreprise` · `Langue` · `Lien Drive CV` · `Lien Drive LM` · `Localisation` · `Raison filtrage` · `Score /10` · `Secteur` · `Source` · `Statut` · `Traité le` · `URL` · `Date envoi candidature` · `Etat du process` · `Type de contrat`

---

## Usage with `veille-emploi` skill

Once recreated, update the skill's `SKILL.md`:
- Replace `YOUR_NOTION_COLLECTION_ID` with your database's collection ID
  (visible in the database URL: `collection://YOUR_ID`)
