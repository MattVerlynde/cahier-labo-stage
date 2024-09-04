---
layout: page
title: .Partie 2 - CLassification avec deep learning
menu:
  main:
    weight: 14
bibFile: content/bibliography.json
toc: True
mermaid: True
---

Cette section présente les résultats de la seconde partie d'expérimentations, sur l'analyse des coûts énergétiques et des performances de modèles sur des applications en classification supervisée par algorithmes basés sur de l'apprentissage profond (_deep learning_).

<!--more-->

## Structure des expériences

### Méthodologie

**Classification multilabel** : chaque image est associée à une ou plusieurs étiquettes.

#### Méthodes

Deep learning, CNN
Deux types d'architecture explorés, en accord avec les travaux présentés en accompagnement de la base de données.
S-CNN (RGB ou All)
InceptionV3 (ici InceptionV2 n'est pas disponible sur PyTorch) 

#### Execution des algorithmes selon des intervalles de paramètres

Les expériences débutent par l'exécution des algorithmes selon des paramètres renseignés en entrée.

- pour le cas de la détection de changement :
```bash
qanat experiment run conso-classif-deep --model [MODEL_NAME] --optim [OPTIMIZER_NAME] --lr [LEARNING_RATE] --loss [LOSS_FUNCTION_NAME] --epochs [NUMBER_OF_EPOCHS] --batch [BATCH_SIZE] --count [0 OU 1] --rgb [0 OU 1]
// or
qanat experiment run conso-classif-deep --param_file [PATH_TO_PARAM_FILE]
```

L'exécution des algorithmes permet la création de plusieurs fichiers en sortie :
- `times.txt` contient des checkpoints de temps clés entres différentes étapes d'exécution des algorithmes : le temps de début d'expérience, le temps de fin de période sans exécution "à froid" et de début d'exécutions "de chauffe" sans mesure, puis les temps de début et fin de chaque répétition d'exécution.
- `output/` contient les fichiers `.npy` de sortie d'algorithmes
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
| Energy             | Variable              | W $\times$ s       | Consommation énergétique de la machine basée sur la mesure de puisance par une prise connectée        |
| Emissions          | Variable              |           | Emissions en $\text{CO}_2$ estimées par le package Python *CodeCarbon*               |
| Memory             | Variable              | % $\times$ s       | Mémoire RAM **libre** lors de l'éxecution                                     |
| Duration           | Variable              | s         | Durée d'exécution                                                             |
| CPU                | Variable              | % $\times$ s       | Consommation de la mémoire du CPU                                             |
| Temperature        | Variable              | °C $\times$ s      | Température interne du CPU                                                    |
| Reads              | Variable              | Nombre de lectures $\times$ s | Nombre de lectures du disque                              |
| Accuracy | Variable |  | Accuracy de classification globale sur chaque étiquette sur l'échantillon de test |
| Recall | Variable |  | Rappel de classification moyen pour chaque étiquette sur l'échantillon de test |
| Modèle | Paramètre |  | Modèle utilisé parmi S-CNN-All, S-CNN-RGB, InceptionV3 from scratch, InceptionV3 préentrainé sur ImageNet (*transfer learning*), InceptionV3 préentrainé sur ImageNet (*fine-tuning*)|
| Epochs | Paramètre |  | Nombre d'époques en entraînement (10, 20, 30) |
| Batch | Paramètre |  | Taille de batch utilisée (100, 200, 300) |
| lr | Paramètre |  | Learning rate (1e-4, 1e-3, 1e-2) |



L'incertitude de la mesure de puissance par la prise connectée est de $\pm 3W$ pour des mesures inférieures à $300W$, et de $\pm 1%$ pour des mesures supérieures.

### Plan d'expérience

X paramètres seront explorés dans ces expériences :
- XX
- XX

Les données utilisées sont tirées de la base de données BigEarthNet (voir page _V. Expérimentations sur les données BigEarthNet_).

A réaliser alors : XXX scénarios

A réaliser alors : XXX exécutions

### Résultats

