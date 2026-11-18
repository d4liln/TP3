# TP3_ID24 : Pipelines IA avec PySpark (Small et Big)

**Auteur :** Dalil NAAMNA · **Identifiant :** 24 · **Module :** Big Data · **Date :** 18/11/2026

Deux notebooks Kaggle repris, corrigés et complétés avec PySpark. Chaque notebook commence par une cellule *Contribution* (le notebook est-il correct, problèmes rencontrés, modifications apportées), et chaque section est étiquetée `## [Étape]` : Data · EDA · Prétraitement · Transformation · ML · Éval · Déploiement.

## Structure
```
TP3_ID24/
├── README.md
├── links          # liens vers les notebooks Kaggle d'origine
├── Small/
│   └── tp3.ipynb      # analyse de sentiments (texte)
└── Big/
    └── tp3_big.ipynb  # classification d'images (≈ 1,5 Go)
```

## Small : analyse de sentiments (BERT ↔ RoBERTa)
- **Origine :** https://www.kaggle.com/code/akruti850/text-model
- **Données :** Malaysia Restaurant Review Datasets (avis Google, plus avis TripAdvisor pour le test hors domaine).
- **Modèle :** fusion BERT + RoBERTa par cross-attention, en modes `frozen` et `finetuned`, sur 3 classes (Negative / Neutral / Positive).
- **Apports :** chemins Kaggle robustes (`find_file`), EDA en échelle log, et **ajout de PySpark** pour la lecture distribuée du CSV, l'EDA (`groupBy`, `percentile_approx`) et le prétraitement (déduplication, sous-échantillonnage équilibré, découpage stratifié 70/10/20 reproductible, export Parquet).
- **État :** rendu sans sorties. Les cellules PySpark ajoutées sont à exécuter sur Kaggle (GPU + dataset ajouté en *Input*).

## Big : classification d'images (Custom CNN vs ResNet-50)
- **Origine :** https://www.kaggle.com/code/ragavendransara/23004926-midsem
- **Données :** Malaysia Food 11 (11 classes × 1 000 images, ≈ 1,5 Go).
- **Apports :** passage de 6 à 11 classes ; PySpark pour l'inventaire (`binaryFile`), l'EDA et un découpage stratifié 70/15/15 reproductible ; gestion de l'image corrompue ; section déploiement (`predict_image`).
- **État :** exécuté de bout en bout sur Kaggle (GPU), sans erreur.

| Modèle | Accuracy test | Meilleure accuracy val |
|---|---|---|
| Custom CNN (from scratch) | 75,7 % | 74,4 % |
| ResNet-50 (transfer learning) | **90,8 %** | 91,4 % |

## Exécution
Les deux notebooks sont prévus pour Kaggle : ajouter le dataset correspondant via *Add Input*, activer le GPU, puis *Run All*. PySpark (et Java 17 si besoin) est installé automatiquement par le notebook.

## Conclusions
- PySpark prend en charge la partie données (inventaire, statistiques, nettoyage, découpage) de façon distribuée et reproductible. PyTorch / Transformers se chargent de l'apprentissage.
- Sur les images, le transfer learning (ResNet-50) dépasse nettement un CNN entraîné de zéro (+15 points en test) et converge dès la 1re époque.
- Sur le texte, le rééquilibrage des classes est indispensable : les avis positifs représentent environ 78 % du corpus.
