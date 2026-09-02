# T-DAT-600 — Joja · Data Visualization

Analyse exploratoire et modèle prédictif sur **3,4 millions de commandes** d'un service
de livraison de courses en ligne.

> **Le résultat en une phrase :** cette activité ne vend pas des produits, elle vend une
> habitude — 59 % des articles commandés sont des rachats, et le panier ne grossit jamais.

---

## Démarrage rapide

```bash
# 1. Environnement
python -m venv .venv
.venv\Scripts\activate          # Windows  (source .venv/bin/activate sous Unix)
pip install -r requirements.txt

# 2. Données  (non versionnées, voir plus bas)
#    Placer les 5 CSV dans 02_projet/data/

# 3. Lancement
jupyter lab
```

Ouvrir ensuite `02_projet/analyse.ipynb` et faire **Run → Restart Kernel and Run All Cells**.

Sans installer quoi que ce soit, le fichier **`02_projet/analyse.html`** contient le
notebook exécuté, graphiques compris — c'est la façon la plus rapide de prendre
connaissance du travail.

## Contenu du dépôt

| Chemin | Description |
|---|---|
| `02_projet/analyse.ipynb` | **Livrable principal** — profilage, EDA, 9 constats, modèle prédictif |
| `02_projet/analyse.html` | Le même notebook exécuté, lisible sans Python |
| `02_projet/soutenance.pptx` | **Support de soutenance PowerPoint** — 18 diapositives, notes de l'orateur dans le volet commentaires |
| `02_projet/soutenance.html` | Le même support en HTML autonome, mode présentateur (touche N) |
| `02_projet/presentation.html` | Première version du support, thème sombre |
| `02_projet/memo_soutenance.pdf` | Mémo de l'orateur : une page par graphique, chiffres et questions du jury |
| `02_projet/T-DAT-600_Joja.pdf` | Sujet du projet |
| `01_bootstrap/bootstrap.ipynb` | Bootstrap préparatoire (encodage, quartet d'Anscombe, dataviz trompeuse) |
| `00_kickoff/` | Slides d'introduction du module |
| `requirements.txt` | Dépendances figées |

## Les données

Les cinq fichiers sources pèsent environ **660 Mo** et ne sont pas versionnés — voir
`.gitignore`. Ils se déposent dans `02_projet/data/` :

| Fichier | Lignes | Contenu |
|---|---:|---|
| `order_products.csv` | 32 434 489 | une ligne par produit dans une commande |
| `orders.csv` | 3 421 083 | une ligne par commande (client, rang, jour, heure, délai) |
| `products.csv` | 49 688 | catalogue produits |
| `aisles.csv` | 134 | libellés des allées |
| `departments.csv` | 21 | libellés des rayons |

Ces tables sont **reliées par des clés**, à la manière d'une base de données. Le notebook
les fusionne en une table unique après contrôle d'intégrité référentielle.

**Deux particularités à connaître**, identifiées lors du profilage. Les 206 209 valeurs
absentes de `days_since_prior_order` correspondent aux *premières* commandes, qui n'ont
par définition pas de précédente. Et 206 209 commandes ne contiennent aucun produit : ce
sont les dernières commandes de chaque client, retirées à l'origine pour servir d'épreuve
d'évaluation. Aucune des deux n'est une erreur.

## Ce que contient l'analyse

Le notebook suit un déroulé constant : **question → méthode → constat chiffré → action**.

**Profilage et nettoyage** — types compacts imposés (division par trois de l'empreinte
mémoire), contrôle d'intégrité, fusion en une table unique de 32,4 millions de lignes.

**Neuf constats**, chacun assorti d'une recommandation. Le panier se fige au lieu de
grossir ; l'ordre d'ajout au panier trahit l'habitude ; les rayons diffèrent d'un facteur
deux dans leur capacité à fidéliser ; le bio pèse trois fois son poids de catalogue ; deux
clientèles se distinguent par leur créneau ; le week-end remplit les paniers ; la cadence
de rachat est dictée par la course bien plus que par le produit ; le rayon d'entrée n'est pas celui de
fidélité. Enfin, un **second mode d'achat** coexiste avec la routine : l'achat de
recette, révélé par le lift des associations de produits et confirmé par les articles très
vendus mais peu rachetés.

**Un modèle prédictif** qui anticipe le contenu de la prochaine commande. Quatre modèles
comparés, découpe par client pour éviter toute fuite, seuil de décision optimisé sur un
jeu de validation, importance des variables mesurée par permutation, et audit du biais
selon l'ancienneté du client.

## Reproductibilité

La graine aléatoire est fixée (`ALEA = 42`) : deux exécutions donnent des résultats
identiques. Le notebook s'exécute de bout en bout sans intervention, en environ **trois
minutes** et **1,5 Go de mémoire vive**.

## Outils

`pandas` pour la manipulation, `numpy` pour le calcul vectorisé, `matplotlib` pour les
graphiques, `scikit-learn` pour la modélisation. La section 0 du notebook justifie chacun
de ces choix face à ses alternatives.
