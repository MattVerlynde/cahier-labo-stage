---
layout: page
title: Partie 1 - Analyse statistique
menu:
  main:
    weight: 13
bibFile: content/bibliography.json
toc: True
mermaid: True
---

Cette section présente les résultats de la première partie d'expérimentations, sur l'analyse des coûts énergétiques et des performances de modèles sur des applications en détection de changement et clustering par algorithmes basés sur des statistiques sans apprentissage.

<!--more-->

## Structure des expériences

### Méthodologie

**Détection de changements**, avec classification binaire : zone avec changement(s), zone sans changement.
**Clustering** avec un nombre de clusters fixé
Les données utilisées sont des images SAR polarisées.

La réalisation des expériences s'effectue en deux temps : l'exécution des algorithmes, puis la récupération des résultats de consommation.

#### Execution des algorithmes selon des intervalles de paramètres

Les expériences débutent par l'exécution des algorithmes selon des paramètres renseignés en entrée.

- pour le cas de la détection de changement :
```bash
qanat experiment run conso-change --image [PATH_TO_IMAGE] --cores [NUMBER_OF_THREADS] --window [WINDOW_SIZE] --robust [0 or 1]
// or
qanat experiment run conso-change --param_file [PATH_TO_PARAM_FILE]
```
- pour le cas du clustering :
```bash
qanat experiment run conso-clustering --image [PATH_TO_IMAGE] --cores [NUMBER_OF_THREADS] --window [WINDOW_SIZE] --riemann [0 or 1]
// or
qanat experiment run conso-clustering --param_file [PATH_TO_PARAM_FILE]
```

L'exécution des algorithmes permet la création de plusieurs fichiers en sortie :
- `times.txt` contient des checkpoints de temps clés entres différentes étapes d'exécution des algorithmes : le temps de début d'expérience, le temps de fin de période sans exécution "à froid" et de début d'exécutions "de chauffe" sans mesure, puis les temps de début et fin de chaque répétition d'exécution.
- `output/` contient les fichiers `.npy` de sortie d'algorithmes (en détection de changement ou en clustering)
- `emissions/` contient les fichiers `.csv` de sortie de mesure de consommation énergétique et d'émissions de carbone du package Python CodeCarbon

#### Récupération des données de consommation et analyse

Enfin, pour réaliser une analyse statistique des données de consommation énergétique et les performances du modèle pour un ensemble d'expériences :
```bash
python performance-tracking/experiments/conso/stats_summary.py [RUN_ID(S)] --grouped/-g [True/False] --file/-f [True/False]
```

Cette commande permet de créer un fichier résultats `output_all.csv` contenant toutes les données de consommation et de performances avec une ligne par exécution si `--file False` en interrogeant la base de données InfluxDB de stockage de données de consommation à partir du fichier `times.txt`, et en calculant les performances des algorithmes en utilisant les fichiers de sorties compris dans `output/`. Les données d'émissions issues des fichiers de `emissions/` sont également prises en compte. A partir du fichier `output_all.csv`, les analyses de résultats sont réalisées et enregistrées sous la forme de graphiques (distribution des données, covariances entre les variables, ACP).
La phase de création du fichier `output_all.csv` pouvant être coûteuse en termes calculatoires, il est possible de réeffectuer les analyses sur un fichier déjà créé via l'option `--file True`.

L'option `--grouped True` permet d'effectuer ces analyses lorsque les exécutions via _Qanat_ ont été réalisées par l'utilisation de `--param_file`.


### Explication des paramètres et des variables

| Variable/Paramètre | Paramètre ou variable | Unité     | Commentaire                                                                   |
|:------------------:|:---------------------:|:---------:|-------------------------------------------------------------------------------|
| Méthode            | Paramètre             |           | Nature de la méthode utilisée parmi _GLRT_ et _robust GLRT_ pour la détection de changement et  _clustering logdet_ et _clustering riemann_ pour le clustering |
| Threads            | Paramètre             | Nombre de threads | Nombre de threads utilisées pour les calculs en parallèle (1 ou 12)   |
| Window             | Paramètre             | Pixels    | Taille de la fenêtre glissante utilisée dans l'estimation  de la matrice de covariance |
| Image              | Paramètre             |           | Série temporelle utilisée                                                     |
| Energy             | Variable              | W.s       | Consommation énergétique de la machine mesurée par une prise connectée        |
| Emissions          | Variable              |           | Emissions en $\text{CO}_2$ estimées par le package Python *CodeCarbon*               |
| Memory             | Variable              | %.s       | Mémoire RAM **libre** lors de l'éxecution                                     |
| Duration           | Variable              | s         | Durée d'exécution                                                             |
| CPU                | Variable              | %.s       | Consommation de la mémoire du CPU                                             |
| Temperature        | Variable              | °C.s      | Température interne du CPU                                                    |
| Reads              | Variable              | Nombre de lectures $\times$ s | Nombre de lectures du disque                              |
| AUC                | Variable (change detection) |           | Aire sous la courbe ROC                                                 |
| Average precision  | Variable (change detection) |           | Moyenne pondérée des précisions de classification atteinte à chaque valeur seuil, l'augmentation du rappel du seuil précédent étant utilisé comme poids |
| Silhouette score        | Variable (clustering)  |           | Moyenne des coefficient de silhouette pour chaque exécution             |
| Calinski-Harabasz score | Variable (clustering)  |           | Rapport de la variance inter-cluster sur la variance intra-cluster      |
| Davies-Bouldin score    | Variable (clustering)  |           | Moyenne du rapport maximal entre la distance d'un point au centre de son groupe et la distance entre deux centres de groupes |

Average precision :
$$AP = \sum_{n} (R_n - R_{n-1}) \times P_n$$

Coefficient de silhouette :
$$CS = \frac{b - a}{max(a, b)}$$  avec $a$ la distance intra-cluster moyenne et $b$ la distance moyenne au cluster le plus proche.


### Plan d'expérience

4 paramètres seront explorés dans ces expériences :
- L'algorithme utilisé (_GLRT_, _robust GLRT_, _clustering logdet_, _clustering riemann_)
- Nombre de threads (1, 12)
- Taille de la fenêtre d'estimation de covariance ($5 \times 5$, $7 \times 7$, $21 \times 21$)
- Données utilisées (*Scene_2small*, *Scene_3small*)

Les données utilisées sont tirées de découpes des images Scene_2 et Scene_3 et de dimension $1000 \times 500$ pixels. *Scene_2small* compte 4 images et *Scene_3small* compte 17 images. La tâche de clustering est effectuée sur la première image uniquement.

A réaliser alors : $4 \times 2 \times 3 \times 2 = 64 \text{ scénarios}$

On observe des temps d'exécution variant fortement selon les paramètres et la méthode utilisée. En effet, la méthode de détection de changement de GLRT robuste tend à nécessiter plus d'une heure d'éxecution sur un ensemble d'images avec parallélisation (et moins de temps sans parallélisation). Au contraire, la méthode GLRT est associée à des temps d'exécution beaucoup plus rapide sur les mêmes données (de l'ordre de quelques dizaines de secondes). Ceci peut représenter un problème dans la récolte des données de consommation, le pas de temps entre deux mesures de consommation électrique étant de 30 secondes.

Ainsi notre choix a été de répéter les expériences sur le modèle de GLRT robuste 10 fois, et 50 fois pour les autres modèles.

A réaliser alors : $16 \times 10 + 48 \times 50 = 2560 \text{ exécutions}$

### Résultats intermédiaires

#### Détection de changement

<iframe src="../conso-change-clustering/intermediaire/change/correlation_matrix.html"
width="600" height="600" style="border: none;"></iframe>

Pour 4 images :
<iframe src="../conso-change-clustering/intermediaire/change/perf_energy_4images.html"
width="1000" height="600" style="border: none;"></iframe>

Pour 17 images :
<iframe src="../conso-change-clustering/intermediaire/change/perf_energy_17images.html"
width="1000" height="600" style="border: none;"></iframe>

<iframe src="../conso-change-clustering/intermediaire/change/pca_circle.html"
width="800" height="400" style="border: none;"></iframe>

{{<warning>}}
L'ACP doit être réalisée plsuieurs fois selon la données utilisée. Dans les résultats suivant, la donnée utilisée apparaît comme une variable prise en compte dans le calcul d'ACP (le nombre d'images). A terme, cette variable ne sera pas prise en compte dans le calcul, mais plusieurs ACP seront réalisées selon cette valeur.
+ **Toutes les exécutions n'ont pas été prise en compte dans l'ACP !** Quelques exécutions trop rapides ayant entraîné une absence de mesure de consommation énergétique ont été ignorées.
{{</warning>}}

Observations :
- Données de consommation (Energy, Emissions, CPU, Temperature) très corrélées entre elles et avec la durée d'exécution
- Données de consommation et nombre d'images corrélées (plus l'algorithme traite d'images, plus il y a d'énergie consommée)
- AUC et métriques de consommation corrélées (plus on consomme, et plus on traite d'images, plus on a de bonnes performances)
- Average precision et AUC corrélées (résultat rassurant, on s'attend à de bonne performances selon les deux métriques)
- Window size totalement plutôt corrélées avec Average precision et sans corrélation avec AUC
- Mémoire libre et nombre de threads plutôt anti-corrélées (plus on parallélise le calcul, plus on utilise de mémoire RAM, ce qui semble cohérent)
- Nombre de threads sans corrélation avec les performances (on obtient les mêmes résultats en sortie d'algorithme que le calcul soit parallélisé ou non : résultat rassurant sur notre implémentation de la parallélisation)


#### Clustering

<iframe src="../conso-change-clustering/intermediaire/clustering/correlation_matrix.html"
width="600" height="600" style="border: none;"></iframe>

<iframe src="../conso-change-clustering/intermediaire/clustering/perf_energy_4images.html"
width="1000" height="600" style="border: none;"></iframe>

<iframe src="../conso-change-clustering/intermediaire/clustering/pca_circle.html"
width="800" height="400" style="border: none;"></iframe>

{{<warning>}}
**Toutes les exécutions n'ont pas été prise en compte dans l'ACP !**
Les exécutions trop rapides ayant entraîné une absence de mesure de consommation énergétique ont été ignorées. Celles-ci correspondent notamment aux mesures avec une taille de fenêtre de $21 \times 21$
{{</warning>}}

Observations :
- Score de Davies-Bouldin anti-corrélé avec la métrique de Calinski-Harabaz et le Silhouette-score : **en accord avec leur définition**, un score de Davies-Bouldin annonce de bonnes performances à 0 et de mauvaises à $+\infty$, à l'inverse des deux autres scores
- Données de consommations très corrélées entre elles (Energy, Emissions, CPU, Duration, Temperature, free memory) **RESULTAT POSSIBLEMENT ANORMAL, les données devraient intuitivement être très corrélées entre elles, à l'exception de la mémoire libre sur la RAM**
    Hypothèse : moins on utilise de mémoire RAM, plus on utilise de mémoire CPU, plus on consomme d'énergie ? **A vérifier**
- Nombre de thread anti-corrélé aux consommations énergétiques (plus on parallélise, moins on consomme)
- Nombre de thread anti-corrélé à la mémoire libre (plus on parallélise, plus on utilise de mémoire) **Résultat normal**
- Nombre de thread anti-corrélé à la taille de la fenêtre **Résultat ANORMAL, il ne devrait pas avoir de corrélation puisqu'on choisit ces paramètres et on teste toutes les combinaisons : ceci est dû aux exécutions manquantes dans ce plot**


