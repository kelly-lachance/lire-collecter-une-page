# Lire et collecter une page web

Collecte automatisée (web scraping) des livres de la catégorie **Science** du site d'entraînement [Books to Scrape](https://books.toscrape.com/catalogue/category/books/science_22/index.html), avec Python, `requests` et BeautifulSoup. Les données sont ensuite nettoyées et analysées avec pandas, puis exportées en CSV.

> Projet réalisé dans le cadre d'une formation Data Analyst. Books to Scrape est un site fictif conçu pour s'exercer au scraping.

## Objectifs

- Analyser la structure d'une page web avec l'inspecteur du navigateur (blocs répétés, sélecteurs CSS).
- Récupérer une page avec `requests` et l'analyser avec BeautifulSoup.
- Parcourir toutes les pages d'une catégorie, puis la fiche détaillée de chaque livre.
- Assembler, nettoyer et analyser les données avec pandas.
- Exporter le résultat dans un fichier CSV.

## Structure du projet

```
lire-collecter-une-page/
├── data/
│   └── livres_science.csv   ← données collectées (14 livres)
├── notebooks/
│   └── collecte.ipynb       ← démarche complète, étape par étape
├── requirements.txt         ← dépendances Python
├── .gitignore
└── README.md
```

## Installation

```bash
git clone https://github.com/kelly-lachance/lire-collecter-une-page.git
cd lire-collecter-une-page

python -m venv .venv
source .venv/Scripts/activate      # Windows (Git Bash)
# source .venv/bin/activate        # macOS / Linux

pip install -r requirements.txt
```

Ouvrir ensuite `notebooks/collecte.ipynb` dans VS Code ou Jupyter, sélectionner l'environnement `.venv` comme noyau, puis exécuter les cellules dans l'ordre.

## Démarche

| Étape | Ce qui est fait |
|:------|:----------------|
| 1. Repérage | Analyse de la page dans l'inspecteur : 14 livres, 1 seule page, bloc répété `article.product_pod`. |
| 2. Une page | Requête HTTP avec un en-tête `User-Agent` identifiant le projet, puis analyse du HTML avec BeautifulSoup. |
| 3. Toutes les pages | Fonction `collecter_toutes_les_pages()` qui suit le lien « next » tant qu'il existe, avec une pause d'une seconde entre deux requêtes. |
| 4. Fiches détaillées | Fonction `lire_detail()` qui visite la fiche de chaque livre pour récupérer la catégorie, le stock et l'UPC. |
| 5. Assemblage et nettoyage | Fusion des données dans un DataFrame, conversion du prix en nombre et extraction du stock chiffré avec une expression régulière. |
| 6. Analyse | Nombre de livres, stock total, prix moyen et maximum, répartition des notes. |
| 7. Export | Écriture du fichier `data/livres_science.csv`. |

### Données collectées

| Donnée | Sélecteur CSS | Contenu ou attribut ? |
|:-------|:--------------|:----------------------|
| titre | `h3 a` | Attribut `title` |
| prix | `p.price_color` | Contenu |
| disponibilité | `p.instock` | Contenu |
| note | `p.star-rating` | Attribut `class` |
| lien vers la fiche | `h3 a` | Attribut `href` |
| catégorie | `ul.breadcrumb li a` (fiche) | Contenu |
| stock, UPC | `table.table-striped tr` (fiche) | Contenu |

## Fichier de sortie

`data/livres_science.csv` contient une ligne par livre :

| Colonne | Description |
|:--------|:------------|
| `titre` | Titre du livre |
| `categorie` | Catégorie (fil d'Ariane de la fiche) |
| `prix_gbp` | Prix tel qu'affiché sur le site (texte, ex. `£42.96`) |
| `note` | Note de 1 à 5 |
| `stock` | Nombre d'exemplaires disponibles |
| `upc` | Identifiant unique du produit |
| `url` | Lien vers la fiche détaillée |
| `prix` | Prix en livres sterling, converti en nombre |

## Résultats

- **14 livres** collectés dans la catégorie Science.
- **105 exemplaires** en stock au total.
- Prix moyen : **33,09 £**. Prix minimum : 10,01 £. Prix maximum : 57,36 £.
- Répartition des notes :

| Note | 1 | 2 | 3 | 4 | 5 |
|:-----|--:|--:|--:|--:|--:|
| Nombre de livres | 4 | 2 | 1 | 5 | 2 |

## Bonnes pratiques de collecte

- Un en-tête `User-Agent` identifie le projet auprès du site.
- Une pause d'une seconde (`time.sleep(1)`) entre chaque requête évite de surcharger le serveur.
- Un `timeout` de 10 secondes empêche le script de rester bloqué.
- Le code HTTP est vérifié avant toute lecture de la page.

## Technologies

Python 3.12 · requests · BeautifulSoup 4 · pandas · Jupyter
