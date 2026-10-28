# Gestion des valeurs manquantes : comparaison de méthodes d'imputation

Projet de **Biostatistique** réalisé en Master 1 (Économétrie et Statistique appliquée), en binôme avec Ly Néné Gallé. Réalisé sous R et Quarto.

## Objectif
Comparer plusieurs méthodes d'imputation sur des données financières d'emprunteurs. La base d'origine étant complète, 15 % des valeurs ont été supprimées aléatoirement. On connaît donc les vraies valeurs et on peut **mesurer l'erreur de chaque méthode**.

## Données
500 emprunteurs, 9 variables quantitatives (âge, revenu mensuel, dépenses mensuelles, épargne, montant, durée, mensualité et taux du prêt, score de crédit) et 4 qualitatives (niveau d'éducation, statut d'emploi, type de prêt, région). Source : Kaggle. Le jeu de données semble synthétique : les résultats sont **illustratifs**.

- `data/DATA.csv` : base complète (vraies valeurs)
- `data/data_na_final.csv` : base avec 15 % de valeurs manquantes (la variable `Region` est épargnée)

## Démarche
1. **Analyse descriptive** de la base avec NA : structure des valeurs manquantes, distributions, corrélations de Spearman, V de Cramér.
2. **Trois méthodes d'imputation**, appliquées à toutes les variables :
   - **A.** médiane (quanti) / mode (quali) par région ;
   - **B.** analyse factorielle de données mixtes (`missMDA::imputeFAMD`, 3 axes) ;
   - **C.** imputation multiple `mice` (5 jeux, moyenne ou modalité majoritaire).
3. **Évaluation sur les vraies valeurs** : NRMSE pour les variables quantitatives, taux de bonne classification pour les qualitatives, plus préservation des distributions et des corrélations.

## Résultats
Évaluation sur les cellules qui étaient manquantes, en comparant aux vraies valeurs :

| | A. Médiane / mode | B. FAMD | C. MICE |
|---|---|---|---|
| NRMSE moyen (variables quantitatives, plus bas = mieux) | 1,04 | 0,93 | 0,92 |
| Précision moyenne (variables qualitatives) | 38,4 % | 38,3 % | 30,1 % |
| Corrélation revenu / dépenses (réelle : 0,83) | 0,67 | 0,83 | 0,87 |

- Les méthodes multivariées (FAMD, MICE) font mieux que la médiane, surtout pour les variables fortement liées entre elles : revenu (NRMSE 0,60 avec MICE), dépenses, épargne, mensualité.
- Pour l'âge, le score de crédit et les variables qualitatives, presque indépendants du reste, aucune méthode ne fait mieux que la moyenne ou le mode (référence qualitative : 40,9 %).
- La médiane par région déforme les distributions et affaiblit les corrélations (revenu / dépenses : 0,83 devient 0,67). FAMD et MICE conservent bien les liens ; FAMD est la plus proche pour revenu / dépenses et revenu / épargne, MICE pour montant / mensualité.

Un NRMSE proche de 1 équivaut à imputer la moyenne.

## Limites
- Valeurs manquantes créées **complètement au hasard** (cas le plus favorable), un seul tirage.
- Données synthétiques (la mensualité est une fonction exacte du montant, du taux et de la durée).

## Contenu du dépôt
| Fichier | Description |
|---|---|
| `imputation_valeurs_manquantes.qmd` | Rapport Quarto (code et texte) |
| `data/` | Bases complète et avec NA |

## Reproduire
Ouvrir le dossier dans RStudio et rendre `imputation_valeurs_manquantes.qmd` avec Quarto (la graine est fixée à 123). Packages : `tidyverse`, `naniar`, `vcd`, `corrplot`, `missMDA`, `mice`, `knitr`.
