# WTTJ Job Scraper — Welcome to the Jungle

Scraper automatisé d'offres d'emploi Data depuis [Welcome to the Jungle](https://www.welcometothejungle.com), avec export structuré dans un fichier Excel.

---

## Description

Ce projet collecte automatiquement les offres d'emploi liées à la **Data** en **France** sur le site Welcome to the Jungle, en utilisant **Selenium** pour piloter un navigateur Chrome réel.
Les données extraites sont ensuite stockées dans un fichier **Excel formaté** prêt à l'emploi.

### Données collectées pour chaque offre

| **Intitulé du poste** | Titre de l'offre (ex: Data Engineer, Data Analyst…) 
| **Entreprise** | Nom de la société qui recrute 
| **Ville** | Localisation du poste 
| **Salaire** | Fourchette salariale si disponible 
| **Description** | Contenu du descriptif du poste 
| **Profil recherché** | Compétences et expériences attendues
| **URL** | Lien direct vers l'offre


## ⚙️ Prérequis

- **Google Chrome** installé sur la machine
- Les dépendances Python listées ci-dessous

---

## 🚀 Installation

### 1. Cloner le dépôt

### 2. Installer les dépendances

pip install selenium openpyxl

## ▶️ Utilisation

python wttj.py

Une fenêtre **Google Chrome va s'ouvrir automatiquement** — c'est normal, **ne pas la fermer** pendant l'exécution.
Elle se fermera seule à la fin du script.

### Résultats

- **`wttj_jobs_data.xlsx`** — fichier Excel avec toutes les offres collectées
- **`scraper.log`** — journal d'exécution détaillé

---

## 🔧 Configuration

Les paramètres sont modifiables en haut du fichier `wttj_scraper.py` :

```python
MAX_OFFERS = 30      # Nombre maximum d'offres à collecter
DELAY      = 2.0     # Délai (en secondes) entre chaque offre
EXCEL_PATH = "wttj_jobs_data.xlsx"  # Nom du fichier de sortie
HEADLESS   = False   # ⚠️ Ne pas passer à True (WTTJ bloque le mode headless)
```

### Changer le mot-clé de recherche

Dans la variable `BASE_URL`, modifier `&query=data` :

```python
BASE_URL = (
    "https://www.welcometothejungle.com/fr/jobs"
    "?refinementList%5Boffices.country_code%5D%5B%5D=FR"
    "&query=data"   # ← changer ici : "machine learning", "python", "analyst"...
)
```

## Architecture du projet

```
wttj-scraper/
│
├── wttj.py      		 # Script principal
├── wttj_jobs_data.xlsx  # Fichier de sortie (généré à l'exécution)
├── scraper.log          # Journal d'exécution (généré à l'exécution)
└── README.md
```

### Structure du code

```
wttj.py
│
├── JobOffer (dataclass)       — Modèle de données d'une offre (titre, entreprise, ville, salaire, description, profil, url)
│
├── init_workbook()            — Crée et formate le fichier Excel
├── append_offer()             — Ajoute une ligne dans l'Excel
│
├── build_driver()             — Configure et démarre ChromeDriver
├── try_selectors()            — Essaie plusieurs sélecteurs CSS
├── clean_text()               — Nettoie les textes parasites de WTTJ
├── extract_company_from_url() — Extrait le nom d'entreprise depuis l'URL
├── extract_city_from_url()    — Extrait la ville depuis l'URL
│
├── get_job_links()            — Collecte les liens des offres sur la page de résultats
├── extract_sections()         — Extrait description et profil recherché
├── scrape_offer()             — Scrape les données d'une fiche offre
│
└── run()                      — Boucle principale
```

---

## Pourquoi Selenium ?

Welcome to the Jungle est une **Single Page Application (SPA)** : son contenu est généré dynamiquement par JavaScript après le chargement initial de la page. Une requête HTTP classique (`requests`) retournerait une page vide sans aucune offre.

Selenium pilote un **vrai navigateur Chrome** qui exécute le JavaScript, exactement comme un utilisateur humain, ce qui permet de récupérer le contenu rendu.

Par ailleurs, WTTJ détecte et **bloque le mode headless** (navigateur invisible) — le script désactive donc ce mode et ouvre Chrome visiblement. Des techniques anti-détection sont également appliquées :

- Suppression du flag `webdriver` via CDP
- User-agent personnalisé
- Désactivation des flags d'automatisation Chrome

---

## ⚠️ Avertissements

- **Légalité** : consultez les [CGU de Welcome to the Jungle](https://www.welcometothejungle.com/fr/pages/terms) et le fichier `robots.txt` avant toute utilisation intensive.
- **Politesse** : un délai de 2 secondes est appliqué entre chaque requête pour ne pas surcharger les serveurs.
- **Stabilité** : les sélecteurs CSS peuvent évoluer si WTTJ modifie son interface. En cas de problème, comparer le HTML rendu avec les sélecteurs du script.

---

## 🛠️ Stack technique

| Outil | Rôle |
|---|---|
| `Python 3.10+` | Langage principal |
| `Selenium 4` | Automatisation du navigateur Chrome |
| `openpyxl` | Création et formatage du fichier Excel |
| `re` | Extraction de données par expressions régulières |
| `logging` | Journal d'exécution |
| `dataclasses` | Modélisation des données |

---
