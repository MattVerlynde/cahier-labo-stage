---
layout: page
title: V. Expérimentations sur les données BigEarthNet
menu:
  main:
    weight: 5
bibFile: content/bibliography.json
toc: True
---

Cette page présente l'application de la stratégie mise en place dans la partie __Mise en place d'outils de suivi de performance__ sur les données BigEarthNet contenant un ensemble d'images Sentinel-1 et Sentinel-2 multimodales.

<!--more-->

## Données

Pour les données Sentinel-2, 590 326 images sont fournies selon 3 formats (120 $\times$ 120 pixels avec une résolution de 10m, 60 $\times$ 60 pixels avec une résoltion de 20m et 20 $\times$ 20 pixels avec une résolution de 60m). Ces images sont issues d'enregistrement effectués entre juin 2017 et mai 2018 réparties dan 10 pays européens (Autriche, Belgique, Finlande, Irlande, Kosovo, Lituanie, Luxembourg Portugal, Serbie et Suisse).

Chaque patch (ensemble d'images sur le même lieu) est labellisé avec plusieurs classes issues des classes de la BD Corine Land Cover 2018.

<iframe src="../bigearthnet/class_distribution.html"
width="1000" height="800" style="border: none;"></iframe>

<iframe src="../bigearthnet/patch_label_visualization.html"
width="1200" height="1000" style="border: none;"></iframe>

## Reproduction des expériences de la littérature

{{<cite "sumbul2019">}} {{<cite "sumbul2021">}}

### Limites des autres bases de données

Souvent non ou peu labellisées, donc échantillon d'entraînement généralement de petite taille en proportion. Aussi souvent plusieurs classes par images, ce qui n'est pas le cas des données utilisées en entrainement lorsque l'on utilise des réseaux de neurones préentrainés sur des bases de données classiques (par exemple ImageNet).

### Expérience de base

Comparaison entre les résultats de classification sur les données BigEarthNet via un réseau de neurones préentrainé sur ImageNet (Inception-V2) et un résau de neurone convolutif classique (CNN) avec une petite architecture. 
Optimizer : Adam
Loss : Cross-entropy sigmoïde

#### CNN classique court

Ce réseau comporte 3 couches de convolution avec 32, 32 et 64 filtres, de taille 5x5, 5x5 et 3x3 respectivement, suivi d'une couche de fully connected et d'une couche de sortie. Une opération de max pooling est effectuée après chaque couche de convolution. Cette architecture a été utilisée sur les bandes RGB des images d'une part (__S-CNN-RGB__), ainsi que sur l'ensemble des bandes disponibles (__S-CNN-All__, celles-ci avec une interpolation cubique sur les bandes de résolution de 20 et 60m).

![Architecture du CNN court](/bigearthnet/S-CNN-All.png)

#### K-BranchCNN

{{<cite "szegedy2015">}}

![Architecture de K-BranchCNN](/bigearthnet/K-BranchCNN.png)

{{<proof "Structure">}}
Structure:

* Branch 10m :
  * Conv2d 5x5 32 stride 1 padding same
  * MaxPool 2x2 stride 2 padding valid
  * Dropout 0.25
  * Conv2d 5x5 32 stride 1 padding same
  * MaxPool 2x2 stride 2 padding valid
  * Dropout 0.25
  * Conv2d 3x3 64 stride 1 padding same
  * Dropout 0.25
  * Flatten
  * Dropout 0.5
  * Fully connected 128
  * ReLU
* Branch 20m :
  * Conv2d 3x3 32 stride 1 padding same
  * MaxPool 2x2 stride 2 padding valid
  * Dropout 0.25
  * Conv2d 3x3 32 stride 1 padding same
  * MaxPool 2x2 stride 2 padding valid
  * Dropout 0.25
  * Conv2d 3x3 64 stride 1 padding same
  * Dropout 0.25
  * Flatten
  * Dropout 0.5
  * Fully connected 128
  * ReLU
* Branch 60m :
  * Conv2d 2x2 32 stride 1 padding same
  * MaxPool 2x2 stride 2 padding valid
  * Dropout 0.25
  * Conv2d 2x2 32 stride 1 padding same
  * MaxPool 2x2 stride 2 padding valid
  * Dropout 0.25
  * Conv2d 2x2 32 stride 1 padding same
  * Dropout 0.25
  * Flatten
  * Dropout 0.5
  * Fully connected 128
  * ReLU
* Concaténation des branches
* Fully connected 128
* ReLU
* Dropout 0.25
* Dropout 0.5
* Fully connected 43
* Sigmoïde
* Seuillage
{{</proof>}}


#### Inception-V2

Les poids de ce réseau ont été fixés et une couche de sortie a été ajoutée à l'architecture pour la classification multi-label.

![Architecture de Inception V2](/bigearthnet/inceptionv2.png)

{{<proof "Structure">}}
Structure :
* Input 224x224x3
* Conv2d 7x7 64 stride 2 padding same (112 x 112 x 64)
* ReLU
* MaxPool 3x3 stride 2 padding valid (56 x 56 x 64)
* Conv2d 1x1 64 stride 1 padding valid (56 x 56 x 64)
* ReLU
* Conv2d 3x3 192 stride 1 padding valid (56 x 56 x 192)
* ReLU
* MaxPool 3x3 stride 2 (28 x 28 x 192)
* Inception(1) (28 x 28 x 256) :
  * Branch 0 : 
    * Conv2d 1x1 64 stride 1 padding valid
    * ReLU
  * Branch 1 :
    * Conv2d 1x1 64 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 64 stride 1 padding valid
    * ReLU
  * Branch 2 :
    * Conv2d 1x1 96 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 128 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 128 stride 1 padding valid
    * ReLU
  * Branch 3 :
    * AvgPool2d 3x3 stride 1 padding valid
    * Conv2d 1x1 32 stride 1 padding valid
    * ReLU
* Inception(2) (28 x 28 x 320) :
  * Branch 0 : 
    * Conv2d 1x1 64 stride 1 padding valid
    * ReLU
  * Branch 1 :
    * Conv2d 1x1 64 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 96 stride 1 padding valid
    * ReLU
  * Branch 2 :
    * Conv2d 1x1 64 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 96 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 96 stride 1 padding valid
    * ReLU
  * Branch 3 :
    * AvgPool2d 3x3 stride 1 padding valid
    * Conv2d 1x1 64 stride 1 padding valid
    * ReLU

* Inception(3a) (14 x 14 x 576) :
  * Branch 0 : 
    * Conv2d 1x1 128 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 160 stride 1 padding valid
    * ReLU
  * Branch 1 :
    * Conv2d 1x1 64 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 96 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 96 stride 1 padding valid
    * ReLU
  * Branch 2 :
    * MaxPool2d 3x3 stride 2 padding valid

* Inception(3b) (14 x 14 x 576) :
  * Branch 0 : 
    * Conv2d 1x1 224 stride 1 padding valid
    * ReLU
  * Branch 1 :
    * Conv2d 1x1 64 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 96 stride 1 padding valid
    * ReLU
  * Branch 2 :
    * Conv2d 1x1 96 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 128 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 128 stride 1 padding valid
    * ReLU
  * Branch 3 :
    * AvgPool2d 3x3 stride 1 padding valid
    * Conv2d 1x1 128 stride 1 padding valid
    * ReLU

* Inception(3c) (14 x 14 x 576) :
  * Branch 0 : 
    * Conv2d 1x1 192 stride 1 padding valid
    * ReLU
  * Branch 1 :
    * Conv2d 1x1 96 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 128 stride 1 padding valid
    * ReLU
  * Branch 2 :
    * Conv2d 1x1 96 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 128 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 128 stride 1 padding valid
    * ReLU
  * Branch 3 :
    * AvgPool2d 3x3 stride 1 padding valid
    * Conv2d 1x1 128 stride 1 padding valid
    * ReLU

* Inception(3d) (14 x 14 x 576) :
  * Branch 0 : 
    * Conv2d 1x1 160 stride 1 padding valid
    * ReLU
  * Branch 1 :
    * Conv2d 1x1 128 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 160 stride 1 padding valid
    * ReLU
  * Branch 2 :
    * Conv2d 1x1 128 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 160 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 160 stride 1 padding valid
    * ReLU
  * Branch 3 :
    * AvgPool2d 3x3 stride 1 padding valid
    * Conv2d 1x1 96 stride 1 padding valid
    * ReLU

* Inception(3e) (14 x 14 x 576) :
  * Branch 0 : 
    * Conv2d 1x1 96 stride 1 padding valid
    * ReLU
  * Branch 1 :
    * Conv2d 1x1 128 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 192 stride 1 padding valid
    * ReLU
  * Branch 2 :
    * Conv2d 1x1 160 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 192 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 192 stride 1 padding valid
    * ReLU
  * Branch 3 :
    * AvgPool2d 3x3 stride 1 padding valid
    * Conv2d 1x1 96 stride 1 padding valid
    * ReLU

* Inception(4a) (7 x 7 x 1024) :
  * Branch 0 : 
    * Conv2d 1x1 128 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 192 stride 2 padding valid
    * ReLU
  * Branch 1 :
    * Conv2d 1x1 192 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 256 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 256 stride 2 padding valid
    * ReLU
  * Branch 2 :
    * MaxPool2d 3x3 stride 2 padding valid

* Inception(4b) (7 x 7 x 1024) :
  * Branch 0 : 
    * Conv2d 1x1 352 stride 1 padding valid
    * ReLU
  * Branch 1 :
    * Conv2d 1x1 192 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 320 stride 1 padding valid
    * ReLU
   * Branch 2 :
    * Conv2d 1x1 160 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 224 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 224 stride 1 padding valid
    * ReLU
  * Branch 3 :
    * AvgPool2d 3x3 stride 1 padding valid
    * Conv2d 1x1 128 stride 1 padding valid
    * ReLU

* Inception(4c) (7 x 7 x 1024) :
  * Branch 0 : 
    * Conv2d 1x1 352 stride 1 padding valid
    * ReLU
  * Branch 1 :
    * Conv2d 1x1 192 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 320 stride 1 padding valid
    * ReLU
   * Branch 2 :
    * Conv2d 1x1 192 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 224 stride 1 padding valid
    * ReLU
    * Conv2d 3x3 224 stride 1 padding valid
    * ReLU
  * Branch 3 :
    * AvgPool2d 3x3 stride 1 padding valid
    * Conv2d 1x1 128 stride 1 padding valid
    * ReLU

* Classification layer (1 x 1 x 1024) :
  * AvgPool2d 7x7 stride 1 padding valid
  * Dropout 0.2
  * Conv2d 1x1 1024 stride 1 padding valid
  * Softmax
{{</proof>}}

#### Prétraitement des données

Elimination de 70 987 images couvertes par des nuages, ombres ou couverts neigeux, puis sélection de 60% des images pour l'entraînement et 20% pour la validation et le test.

Nombre d'épochs fixé à 100, et algorithme de descente de gradient stochastique utilisé, avec minimisation de la loss de cross-entropie sigmoïde. Learing rate : 0.001.

Métriques de performances utilisées: F1-score (F1), F2-score (F2), précision (P) et rappel (R).

#### Plan d'exécution

1. Téléchargement des données : les données Sentinel-2 et Sentinel-1 sont téléchargées depuis le site de BigEarthNet. Les deux bases de données sont nécessaires pour l'utilisation du code fourni (mais les données Sentinel-1 ne sont pas utilisées dans cette expérience).

2. Prétraitement des données : les données sont prétraitées pour les rendre utilisables par les réseaux de neurones. Celles-ci sont divisées en 3 échantillons prédéfinis d'entraînement, de validation et d'évaluation, et enregistrés sous le format Tensorflow Record.

3. Téléchargement des modèles : les modèles pré-entrainés sont téléchargés (Inception-V2, S-CNN-All, ResNet50).

4. Entraînement des modèles : les modèles pour lesquels les poids ne sont pas fournis sont entraînés sur les données d'entraînement (S-CNN-RGB). De même, les modèles pour lesquels les poids sont fournis mais non entrainés sur les données BigEarthNet sont fine-tuned sur les données d'entraînement (Inception-V2).

5. Evaluation des modèles : les modèles sont évalués sur les données de test.

6. Analyse des résultats : les résultats sont analysés et comparés à ceux de la littérature.

#### Paramètres

| Simulation | Modèle | Optimiseur | Fonction de coût | Taux d'apprentissage | Taille de batch | Epoques | Echantillon d'apprentissage | Echantillon de test | 
|:-----:|------------------|------|------------------------|:-----:|:---:|:---:|:----------------:|:------------:|
| **1** | **S-CNN-All**    | Adam | Cross-entropy sigmoïde | 0.001 | 500 | 50  | **5%** (26969)   | 25% (125866) |
| **2** | **S-CNN-All**    | Adam | Cross-entropy sigmoïde | 0.001 | 500 | 150 | **5%** (26969)   | 25% (125866) |
| **3** | **S-CNN-All**    | Adam | Cross-entropy sigmoïde | 0.001 | 500 | 100 | **0.5%** (2697)  | 25% (125866) |
| **4** | **S-CNN-RGB**    | Adam | Cross-entropy sigmoïde | 0.001 | 500 | 100 | **0.5%** (2697)  | 25% (125866) |
| **5** | **Inception-V2** | Adam | Cross-entropy sigmoïde | 0.001 | 500 | 100 | **0.5%** (2697)  | 25% (125866) |
| **6** | **Inception-V2** | Adam | Cross-entropy sigmoïde | 0.001 | 500 | 100 | **0.05%** (270)  | 25% (125866) |
| **7** | **Inception-V2** | Adam | Cross-entropy sigmoïde | 0.001 | 500 | 100 | **5%** (26969)   | 25% (125866) |
| **8** | **ResNet50**     | Adam | Cross-entropy sigmoïde | 0.001 | 500 | 100 | **50%** (269695) | 25% (125866) |
| **9** | **K-BranchCNN**  | Adam | Cross-entropy sigmoïde | 0.001 | 500 | 100 | **50%** (269695) | 25% (125866) |

Sur des données échantillonnées aléatoirement :

| Simulation | Modèle | Optimiseur | Fonction de coût | Taux d'apprentissage | Taille de batch | Epoques | Echantillon d'apprentissage | Echantillon de validation | Echantillon de test |
|:-----:|------------------|------|------------------------|:------:|:---:|:---:|:--------------:|:------------:|:------------:|
| **A** | **S-CNN-All**    | Adam | Cross-entropy sigmoïde | 0.001  | 500 | 50  | **5%** (26969) | 25% (123723) | 25% (125866) |
| **B** | **S-CNN-All**    | Adam | Cross-entropy sigmoïde | 0.0001 | 500 | 50  | **5%** (26969) | 25% (123723) | 25% (125866) |

#### Caractéristiques computationnelles

CPU : 12th Gen Intel(R) Core(TM) i5-12600
GPU : NVIDIA T400 4GB
RAM : 64 GiB


#### Résultats

Résultats de nos expériences :

| Simulation            | Précision (%) | Rappel (%) | F1     | F2     | F0.5   | Durée d'exécution (s) |
|-----------------------|:-------------:|:----------:|:------:|:------:|:------:|-----------------------|
| **S-CNN-All (1)**     | 72.57         | 56.01      | 0.5994 | 0.5700 | 0.6551 | 12797.3266            |
| **S-CNN-All (2)**     | 74.11         | 53.89      | 0.5889 | 0.5528 | 0.6552 | 22205.8385            |
| **S-CNN-All (3a)**    | 61.38         | 43.07      | 0.4746 | 0.4427 | 0.5343 | 2262.1319             |
| **S-CNN-All (3b)**    | 63.46         | 46.90      | 0.5058 | 0.4780 | 0.5607 | 2314.8287             |
| **S-CNN-All (3c)**    | 63.79         | 47.32      | 0.5116 | 0.4828 | 0.5665 | 2286.6310             |
| **S-CNN-RGB (4a)**    | 55.14         | 36.03      | 0.4058 | 0.3733 | 0.4668 | 1706.7541             |
| **S-CNN-RGB (4b)**    | 51.39         | 31.54      | 0.3628 | 0.3295 | 0.4252 | 1863.3753             |
| **S-CNN-RGB (4c)**    | 54.17         | 37.56      | 0.4139 | 0.3857 | 0.4679 | 1869.9280             |
| **Inception V2 (5a)** | 26.51         | 21.29      | 0.1671 | 0.1666 | 0.2022 | 2248.8830             |
| **Inception V2 (5b)** | 27.15         | 22.40      | 0.1961 | 0.1989 | 0.2220 | 2274.6610             |
| **Inception V2 (5c)** | 20.65         | 19.20      | 0.1452 | 0.1523 | 0.1653 | 2263.2047             |
| **Inception V2 (6)**  | 6.14          | 37.19      | 0.1012 | 0.1707 | 0.0728 | 237.2814              |
| **Inception V2 (7)**  | 70.58         | 50.32      | 0.5566 | 0.5189 | 0.6228 | 22888.0167            |
| **ResNet50 (8)**      | 79.35         | 75.39      | 0.7500 | 0.7470 | 0.7685 | Pré-entrainé          |
| **K-BranchCNN (9)**   | 76.51         | 77.01      | 0.7435 | 0.7530 | 0.7497 | Pré-entrainé          |

| Simulation            | Précision (%) | Rappel (%) | F1     | F2     | F0.5   | Durée d'exécution (s) |
|-----------------------|:-------------:|:----------:|:------:|:------:|:------:|-----------------------|
| **S-CNN-All (A)**     |  |  |  |  |  |  |
| **S-CNN-All (B)**     |  |  |  |  |  |  |

Résultats de la littérature {{<cite "sumbul2019">}}, réalisé sur **l'ensemble de la base de données BigEarthNet** :

| Méthode              | Précision (%) | Rappel (%) | F1     | F2     |
|----------------------|:-------------:|:----------:|:------:|:------:|
| **Inception-v2**     | 48.23         | 56.79      | 0.4988 | 0.5301 |
| **S-CNN-RGB**        | 65.06         | 75.57      | 0.6759 | 0.7139 |
| **S-CNN-All**        | 69.93         | 77.10      | 0.7098 | 0.7384 |

Evolution de la fonction de coût au cours de l'entraînement pour le modèle S-CNN-All (3) :
<iframe src="../bigearthnet/training_losses_S-CNN-All_epochs.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle S-CNN-RGB (4) :
<iframe src="../bigearthnet/training_losses_S-CNN-RGB_epochs.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle InceptionV2 (5) :
<iframe src="../bigearthnet/training_losses_InceptionV2_epochs.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle S-CNN-All (A) :
<iframe src="../bigearthnet/training_losses_S-CNN-All_epochs_val_lr3.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle S-CNN-All (B) :
<iframe src="../bigearthnet/training_losses_S-CNN-All_epochs_val_lr4.html"
width="1000" height="500" style="border: none;"></iframe>

<!--
S-CNN-All (3a)

sample_precision 0.61382806
sample_recall 0.43072605
sample_f1_score 0.47456315
sample_f2_score 0.44266433
sample_f0_5_score 0.53433305
hamming_loss 0.0567368
subset_accuracy 0.16747175
sample_accuracy 0.38674834
one_error 0.2959894
coverage 8.899782
label_cardinality 2.9340966
ranking_loss 0.070368215
label_ranking_avg_precision 0.69781655
micro_precision 0.6582204458546904
macro_precision_class_avg 0.19147747917592578
micro_recall 0.35050622388661884
macro_recall_class_avg 0.09416580856287962
micro_f1_score 0.45742889502596473
micro_f2_score 0.386658326701986
micro_f0_5_score 0.5599099251077273
micro_accuracy 0.2965366676135583
macro_accuracy_class_avg 0.07924987197706809
macro_precision_class0 0.0
macro_precision_class1 0.6108022239872914
macro_precision_class2 0.0
macro_precision_class3 0.0
macro_precision_class4 0.0
macro_precision_class5 0.0
macro_precision_class6 0.0
macro_precision_class7 0.0
macro_precision_class8 0.0
macro_precision_class9 0.0
macro_precision_class10 0.0
macro_precision_class11 0.7296136021851886
macro_precision_class12 0.0
macro_precision_class13 0.0
macro_precision_class14 0.0
macro_precision_class15 0.0
macro_precision_class16 0.0
macro_precision_class17 0.8061025368103601
macro_precision_class18 0.0
macro_precision_class19 0.5009372071227741
macro_precision_class20 0.4280610587853199
macro_precision_class21 0.6666666666666666
macro_precision_class22 0.5455612045365663
macro_precision_class23 0.9206665808776219
macro_precision_class24 0.75713488242272
macro_precision_class25 0.0
macro_precision_class26 0.0
macro_precision_class27 0.0
macro_precision_class28 0.5544590421989466
macro_precision_class29 0.0
macro_precision_class30 0.0
macro_precision_class31 0.0
macro_precision_class32 0.0
macro_precision_class33 0.0
macro_precision_class34 0.0
macro_precision_class35 0.0
macro_precision_class36 0.0
macro_precision_class37 0.0
macro_precision_class38 0.0
macro_precision_class39 0.7440132122213047
macro_precision_class40 0.0
macro_precision_class41 0.0
macro_precision_class42 0.9695133867500488
macro_recall_class0 0.0
macro_recall_class1 0.09773767158108795
macro_recall_class2 0.0
macro_recall_class3 0.0
macro_recall_class4 0.0
macro_recall_class5 0.0
macro_recall_class6 0.0
macro_recall_class7 0.0
macro_recall_class8 0.0
macro_recall_class9 0.0
macro_recall_class10 0.0
macro_recall_class11 0.5307776462970819
macro_recall_class12 0.0
macro_recall_class13 0.0
macro_recall_class14 0.0
macro_recall_class15 0.0
macro_recall_class16 0.0
macro_recall_class17 0.18800165494414564
macro_recall_class18 0.0
macro_recall_class19 0.12508776035572197
macro_recall_class20 0.45232746786471983
macro_recall_class21 0.0008263324610935132
macro_recall_class22 0.6130969821271609
macro_recall_class23 0.36195993119498127
macro_recall_class24 0.5089233366947304
macro_recall_class25 0.0
macro_recall_class26 0.0
macro_recall_class27 0.0
macro_recall_class28 0.25290657535003175
macro_recall_class29 0.0
macro_recall_class30 0.0
macro_recall_class31 0.0
macro_recall_class32 0.0
macro_recall_class33 0.0
macro_recall_class34 0.0
macro_recall_class35 0.0
macro_recall_class36 0.0
macro_recall_class37 0.0
macro_recall_class38 0.0
macro_recall_class39 0.06454154727793696
macro_recall_class40 0.0
macro_recall_class41 0.0
macro_recall_class42 0.8529428620551321
macro_f1_score_class0 0.0
macro_f1_score_class1 0.16851101128519777
macro_f1_score_class2 0.0
macro_f1_score_class3 0.0
macro_f1_score_class4 0.0
macro_f1_score_class5 0.0
macro_f1_score_class6 0.0
macro_f1_score_class7 0.0
macro_f1_score_class8 0.0
macro_f1_score_class9 0.0
macro_f1_score_class10 0.0
macro_f1_score_class11 0.6145117096624102
macro_f1_score_class12 0.0
macro_f1_score_class13 0.0
macro_f1_score_class14 0.0
macro_f1_score_class15 0.0
macro_f1_score_class16 0.0
macro_f1_score_class17 0.30489482336363943
macro_f1_score_class18 0.0
macro_f1_score_class19 0.200187265917603
macro_f1_score_class20 0.43985983222341896
macro_f1_score_class21 0.0016506189821182944
macro_f1_score_class22 0.5773608332758502
macro_f1_score_class23 0.5196281366888187
macro_f1_score_class24 0.6086980912444077
macro_f1_score_class25 0.0
macro_f1_score_class26 0.0
macro_f1_score_class27 0.0
macro_f1_score_class28 0.34736762251555153
macro_f1_score_class29 0.0
macro_f1_score_class30 0.0
macro_f1_score_class31 0.0
macro_f1_score_class32 0.0
macro_f1_score_class33 0.0
macro_f1_score_class34 0.0
macro_f1_score_class35 0.0
macro_f1_score_class36 0.0
macro_f1_score_class37 0.0
macro_f1_score_class38 0.0
macro_f1_score_class39 0.11877924988464834
macro_f1_score_class40 0.0
macro_f1_score_class41 0.0
macro_f1_score_class42 0.9075
macro_f1_score_class_avg 0.11183602779171312
macro_f2_score_class0 0.0
macro_f2_score_class1 0.11747273227215789
macro_f2_score_class2 0.0
macro_f2_score_class3 0.0
macro_f2_score_class4 0.0
macro_f2_score_class5 0.0
macro_f2_score_class6 0.0
macro_f2_score_class7 0.0
macro_f2_score_class8 0.0
macro_f2_score_class9 0.0
macro_f2_score_class10 0.0
macro_f2_score_class11 0.5613750891427653
macro_f2_score_class12 0.0
macro_f2_score_class13 0.0
macro_f2_score_class14 0.0
macro_f2_score_class15 0.0
macro_f2_score_class16 0.0
macro_f2_score_class17 0.22205498597496018
macro_f2_score_class18 0.0
macro_f2_score_class19 0.14717220111239607
macro_f2_score_class20 0.44725655089864697
macro_f2_score_class21 0.0010325956011427392
macro_f2_score_class22 0.5982844889206577
macro_f2_score_class23 0.4119594633500317
macro_f2_score_class24 0.5446327286599139
macro_f2_score_class25 0.0
macro_f2_score_class26 0.0
macro_f2_score_class27 0.0
macro_f2_score_class28 0.2837736503864007
macro_f2_score_class29 0.0
macro_f2_score_class30 0.0
macro_f2_score_class31 0.0
macro_f2_score_class32 0.0
macro_f2_score_class33 0.0
macro_f2_score_class34 0.0
macro_f2_score_class35 0.0
macro_f2_score_class36 0.0
macro_f2_score_class37 0.0
macro_f2_score_class38 0.0
macro_f2_score_class39 0.07896443532979264
macro_f2_score_class40 0.0
macro_f2_score_class41 0.0
macro_f2_score_class42 0.8739591529942335
macro_f2_score_class_avg 0.09971949010797905
macro_f0_5_score_class0 0.0
macro_f0_5_score_class1 0.29796962182269066
macro_f0_5_score_class2 0.0
macro_f0_5_score_class3 0.0
macro_f0_5_score_class4 0.0
macro_f0_5_score_class5 0.0
macro_f0_5_score_class6 0.0
macro_f0_5_score_class7 0.0
macro_f0_5_score_class8 0.0
macro_f0_5_score_class9 0.0
macro_f0_5_score_class10 0.0
macro_f0_5_score_class11 0.6787592578845484
macro_f0_5_score_class12 0.0
macro_f0_5_score_class13 0.0
macro_f0_5_score_class14 0.0
macro_f0_5_score_class15 0.0
macro_f0_5_score_class16 0.0
macro_f0_5_score_class17 0.48632218844984804
macro_f0_5_score_class18 0.0
macro_f0_5_score_class19 0.31290247043671704
macro_f0_5_score_class20 0.4327037868296643
macro_f0_5_score_class21 0.004111278607646978
macro_f0_5_score_class22 0.5578512396694215
macro_f0_5_score_class23 0.7034906588003933
macro_f0_5_score_class24 0.6898448079649562
macro_f0_5_score_class25 0.0
macro_f0_5_score_class26 0.0
macro_f0_5_score_class27 0.0
macro_f0_5_score_class28 0.4476969856959884
macro_f0_5_score_class29 0.0
macro_f0_5_score_class30 0.0
macro_f0_5_score_class31 0.0
macro_f0_5_score_class32 0.0
macro_f0_5_score_class33 0.0
macro_f0_5_score_class34 0.0
macro_f0_5_score_class35 0.0
macro_f0_5_score_class36 0.0
macro_f0_5_score_class37 0.0
macro_f0_5_score_class38 0.0
macro_f0_5_score_class39 0.23957668581152947
macro_f0_5_score_class40 0.0
macro_f0_5_score_class41 0.0
macro_f0_5_score_class42 0.9437180576515796
macro_f0_5_score_class_avg 0.13476621022383686
macro_accuracy_class0 0.0
macro_accuracy_class1 0.0920076573342905
macro_accuracy_class2 0.0
macro_accuracy_class3 0.0
macro_accuracy_class4 0.0
macro_accuracy_class5 0.0
macro_accuracy_class6 0.0
macro_accuracy_class7 0.0
macro_accuracy_class8 0.0
macro_accuracy_class9 0.0
macro_accuracy_class10 0.0
macro_accuracy_class11 0.4435343942984011
macro_accuracy_class12 0.0
macro_accuracy_class13 0.0
macro_accuracy_class14 0.0
macro_accuracy_class15 0.0
macro_accuracy_class16 0.0
macro_accuracy_class17 0.17986779084035942
macro_accuracy_class18 0.0
macro_accuracy_class19 0.11122671938403912
macro_accuracy_class20 0.2819360986329075
macro_accuracy_class21 0.0008259911894273128
macro_accuracy_class22 0.4058378588052754
macro_accuracy_class23 0.35101189746105727
macro_accuracy_class24 0.437502520059675
macro_accuracy_class25 0.0
macro_accuracy_class26 0.0
macro_accuracy_class27 0.0
macro_accuracy_class28 0.21019049804911635
macro_accuracy_class29 0.0
macro_accuracy_class30 0.0
macro_accuracy_class31 0.0
macro_accuracy_class32 0.0
macro_accuracy_class33 0.0
macro_accuracy_class34 0.0
macro_accuracy_class35 0.0
macro_accuracy_class36 0.0
macro_accuracy_class37 0.0
macro_accuracy_class38 0.0
macro_accuracy_class39 0.06313945339873861
macro_accuracy_class40 0.0
macro_accuracy_class41 0.0
macro_accuracy_class42 0.8306636155606407

S-CNN-All (3b)

sample_precision 0.6345588
sample_recall 0.46902713
sample_f1_score 0.50584984
sample_f2_score 0.4779593
sample_f0_5_score 0.560742
hamming_loss 0.05531134
subset_accuracy 0.17857881
sample_accuracy 0.4148095
one_error 0.29007834
coverage 9.631338
label_cardinality 2.9340966
ranking_loss 0.075884596
label_ranking_avg_precision 0.6979306
micro_precision 0.661591775441839
macro_precision_class_avg 0.1936160789436558
micro_recall 0.3877168612223567
macro_recall_class_avg 0.10593147819736895
micro_f1_score 0.4889129425262238
micro_f2_score 0.42271454863114116
micro_f0_5_score 0.5796949481094378
micro_accuracy 0.3235504798403771
macro_accuracy_class_avg 0.0873364453547051
macro_precision_class0 0.0
macro_precision_class1 0.593849080532657
macro_precision_class2 0.0
macro_precision_class3 0.0
macro_precision_class4 0.0
macro_precision_class5 0.0
macro_precision_class6 0.0
macro_precision_class7 0.0
macro_precision_class8 0.0
macro_precision_class9 0.0
macro_precision_class10 0.0
macro_precision_class11 0.7137068149550402
macro_precision_class12 0.2
macro_precision_class13 0.0
macro_precision_class14 0.0
macro_precision_class15 0.0
macro_precision_class16 0.0
macro_precision_class17 0.7909971789525329
macro_precision_class18 0.0
macro_precision_class19 0.5155456852791879
macro_precision_class20 0.46044819000191534
macro_precision_class21 0.7843530591775326
macro_precision_class22 0.5436285628398874
macro_precision_class23 0.892150803461063
macro_precision_class24 0.7428480811925151
macro_precision_class25 0.0
macro_precision_class26 0.0
macro_precision_class27 0.0
macro_precision_class28 0.5191632835615496
macro_precision_class29 0.0
macro_precision_class30 0.0
macro_precision_class31 0.0
macro_precision_class32 0.0
macro_precision_class33 0.0
macro_precision_class34 0.0
macro_precision_class35 0.0
macro_precision_class36 0.0
macro_precision_class37 0.0
macro_precision_class38 0.0
macro_precision_class39 0.7281399046104928
macro_precision_class40 0.0
macro_precision_class41 0.0
macro_precision_class42 0.8406607500128251
macro_recall_class0 0.0
macro_recall_class1 0.1190264361972547
macro_recall_class2 0.0
macro_recall_class3 0.0
macro_recall_class4 0.0
macro_recall_class5 0.0
macro_recall_class6 0.0
macro_recall_class7 0.0
macro_recall_class8 0.0
macro_recall_class9 0.0
macro_recall_class10 0.0
macro_recall_class11 0.538726026479716
macro_recall_class12 0.0006056935190793458
macro_recall_class13 0.0
macro_recall_class14 0.0
macro_recall_class15 0.0
macro_recall_class16 0.0
macro_recall_class17 0.26681836988001656
macro_recall_class18 0.0
macro_recall_class19 0.19014743739761292
macro_recall_class20 0.30001247972045425
macro_recall_class21 0.10769866409585456
macro_recall_class22 0.5828596542631116
macro_recall_class23 0.5112061115046038
macro_recall_class24 0.5492835533875847
macro_recall_class25 0.0
macro_recall_class26 0.0
macro_recall_class27 0.0
macro_recall_class28 0.4167241998287813
macro_recall_class29 0.0
macro_recall_class30 0.0
macro_recall_class31 0.0
macro_recall_class32 0.0
macro_recall_class33 0.0
macro_recall_class34 0.0
macro_recall_class35 0.0
macro_recall_class36 0.0
macro_recall_class37 0.0
macro_recall_class38 0.0
macro_recall_class39 0.032808022922636106
macro_recall_class40 0.0
macro_recall_class41 0.0
macro_recall_class42 0.9391369132901599
macro_f1_score_class0 0.0
macro_f1_score_class1 0.19830598200105876
macro_f1_score_class2 0.0
macro_f1_score_class3 0.0
macro_f1_score_class4 0.0
macro_f1_score_class5 0.0
macro_f1_score_class6 0.0
macro_f1_score_class7 0.0
macro_f1_score_class8 0.0
macro_f1_score_class9 0.0
macro_f1_score_class10 0.0
macro_f1_score_class11 0.6139929004923852
macro_f1_score_class12 0.0012077294685990338
macro_f1_score_class13 0.0
macro_f1_score_class14 0.0
macro_f1_score_class15 0.0
macro_f1_score_class16 0.0
macro_f1_score_class17 0.39903474306221576
macro_f1_score_class18 0.0
macro_f1_score_class19 0.27782526927679946
macro_f1_score_class20 0.36330663442647726
macro_f1_score_class21 0.18939210462581738
macro_f1_score_class22 0.5625609773341063
macro_f1_score_class23 0.6499742699086581
macro_f1_score_class24 0.6315675938035081
macro_f1_score_class25 0.0
macro_f1_score_class26 0.0
macro_f1_score_class27 0.0
macro_f1_score_class28 0.4623374235948343
macro_f1_score_class29 0.0
macro_f1_score_class30 0.0
macro_f1_score_class31 0.0
macro_f1_score_class32 0.0
macro_f1_score_class33 0.0
macro_f1_score_class34 0.0
macro_f1_score_class35 0.0
macro_f1_score_class36 0.0
macro_f1_score_class37 0.0
macro_f1_score_class38 0.0
macro_f1_score_class39 0.06278703132497086
macro_f1_score_class40 0.0
macro_f1_score_class41 0.0
macro_f1_score_class42 0.8871744897406746
macro_f1_score_class_avg 0.12324342207116522
macro_f2_score_class0 0.0
macro_f2_score_class1 0.14168356077339708
macro_f2_score_class2 0.0
macro_f2_score_class3 0.0
macro_f2_score_class4 0.0
macro_f2_score_class5 0.0
macro_f2_score_class6 0.0
macro_f2_score_class7 0.0
macro_f2_score_class8 0.0
macro_f2_score_class9 0.0
macro_f2_score_class10 0.0
macro_f2_score_class11 0.5665041978926015
macro_f2_score_class12 0.0007565441065214102
macro_f2_score_class13 0.0
macro_f2_score_class14 0.0
macro_f2_score_class15 0.0
macro_f2_score_class16 0.0
macro_f2_score_class17 0.3075844438297101
macro_f2_score_class18 0.0
macro_f2_score_class19 0.2176183844011142
macro_f2_score_class20 0.3224854452284496
macro_f2_score_class21 0.13015545421257615
macro_f2_score_class22 0.5745669013881106
macro_f2_score_class23 0.558939041929417
macro_f2_score_class24 0.5794828149277069
macro_f2_score_class25 0.0
macro_f2_score_class26 0.0
macro_f2_score_class27 0.0
macro_f2_score_class28 0.43384509228911505
macro_f2_score_class29 0.0
macro_f2_score_class30 0.0
macro_f2_score_class31 0.0
macro_f2_score_class32 0.0
macro_f2_score_class33 0.0
macro_f2_score_class34 0.0
macro_f2_score_class35 0.0
macro_f2_score_class36 0.0
macro_f2_score_class37 0.0
macro_f2_score_class38 0.0
macro_f2_score_class39 0.04055322389275532
macro_f2_score_class40 0.0
macro_f2_score_class41 0.0
macro_f2_score_class42 0.9176382309131024
macro_f2_score_class_avg 0.11143751943685064
macro_f0_5_score_class0 0.0
macro_f0_5_score_class1 0.3303117945823928
macro_f0_5_score_class2 0.0
macro_f0_5_score_class3 0.0
macro_f0_5_score_class4 0.0
macro_f0_5_score_class5 0.0
macro_f0_5_score_class6 0.0
macro_f0_5_score_class7 0.0
macro_f0_5_score_class8 0.0
macro_f0_5_score_class9 0.0
macro_f0_5_score_class10 0.0
macro_f0_5_score_class11 0.6701718132884497
macro_f0_5_score_class12 0.002992220227408737
macro_f0_5_score_class13 0.0
macro_f0_5_score_class14 0.0
macro_f0_5_score_class15 0.0
macro_f0_5_score_class16 0.0
macro_f0_5_score_class17 0.5678736219224402
macro_f0_5_score_class18 0.0
macro_f0_5_score_class19 0.3840881157227947
macro_f0_5_score_class20 0.4159601342699934
macro_f0_5_score_class21 0.3475864521290781
macro_f0_5_score_class22 0.5510465258002681
macro_f0_5_score_class23 0.7764330720762256
macro_f0_5_score_class24 0.6939399505809991
macro_f0_5_score_class25 0.0
macro_f0_5_score_class26 0.0
macro_f0_5_score_class27 0.0
macro_f0_5_score_class28 0.49483521888834237
macro_f0_5_score_class29 0.0
macro_f0_5_score_class30 0.0
macro_f0_5_score_class31 0.0
macro_f0_5_score_class32 0.0
macro_f0_5_score_class33 0.0
macro_f0_5_score_class34 0.0
macro_f0_5_score_class35 0.0
macro_f0_5_score_class36 0.0
macro_f0_5_score_class37 0.0
macro_f0_5_score_class38 0.0
macro_f0_5_score_class39 0.1389900461277009
macro_f0_5_score_class40 0.0
macro_f0_5_score_class41 0.0
macro_f0_5_score_class42 0.8586684272853984
macro_f0_5_score_class_avg 0.1449511021604998
macro_accuracy_class0 0.0
macro_accuracy_class1 0.11006640418405124
macro_accuracy_class2 0.0
macro_accuracy_class3 0.0
macro_accuracy_class4 0.0
macro_accuracy_class5 0.0
macro_accuracy_class6 0.0
macro_accuracy_class7 0.0
macro_accuracy_class8 0.0
macro_accuracy_class9 0.0
macro_accuracy_class10 0.0
macro_accuracy_class11 0.44299405155320554
macro_accuracy_class12 0.0006042296072507553
macro_accuracy_class13 0.0
macro_accuracy_class14 0.0
macro_accuracy_class15 0.0
macro_accuracy_class16 0.0
macro_accuracy_class17 0.24924634768493467
macro_accuracy_class18 0.0
macro_accuracy_class19 0.1613223468678646
macro_accuracy_class20 0.22197599261311174
macro_accuracy_class21 0.10460139111824505
macro_accuracy_class22 0.3913633680897108
macro_accuracy_class23 0.48145324597974987
macro_accuracy_class24 0.46152633549429545
macro_accuracy_class25 0.0
macro_accuracy_class26 0.0
macro_accuracy_class27 0.0
macro_accuracy_class28 0.3006754737282563
macro_accuracy_class29 0.0
macro_accuracy_class30 0.0
macro_accuracy_class31 0.0
macro_accuracy_class32 0.0
macro_accuracy_class33 0.0
macro_accuracy_class34 0.0
macro_accuracy_class35 0.0
macro_accuracy_class36 0.0
macro_accuracy_class37 0.0
macro_accuracy_class38 0.0
macro_accuracy_class39 0.032411011251857615
macro_accuracy_class40 0.0
macro_accuracy_class41 0.0
macro_accuracy_class42 0.7972269520797859

S-CNN-All (3c)

sample_precision 0.63793
sample_recall 0.47318333
sample_f1_score 0.5115979
sample_f2_score 0.48282942
sample_f0_5_score 0.5664527
hamming_loss 0.055088125
subset_accuracy 0.17582986
sample_accuracy 0.41852334
one_error 0.27776366
coverage 9.128669
label_cardinality 2.9340966
ranking_loss 0.070174515
label_ranking_avg_precision 0.70815194
micro_precision 0.6597299857674573
macro_precision_class_avg 0.202081599831602
micro_recall 0.3978873716162609
macro_recall_class_avg 0.10869151294919238
micro_f1_score 0.49639546510999416
micro_f2_score 0.43219441145310344
micro_f0_5_score 0.5829978757660376
micro_accuracy 0.33013698322365537
macro_accuracy_class_avg 0.09058471503519916
macro_precision_class0 0.0
macro_precision_class1 0.5884715331915898
macro_precision_class2 0.0
macro_precision_class3 0.0
macro_precision_class4 0.0
macro_precision_class5 0.0
macro_precision_class6 0.0
macro_precision_class7 0.0
macro_precision_class8 0.0
macro_precision_class9 0.0
macro_precision_class10 0.0
macro_precision_class11 0.7054069979516822
macro_precision_class12 0.0
macro_precision_class13 0.0
macro_precision_class14 0.0
macro_precision_class15 0.0
macro_precision_class16 0.0
macro_precision_class17 0.7569721115537849
macro_precision_class18 0.0
macro_precision_class19 0.4772879954524314
macro_precision_class20 0.4509951798537561
macro_precision_class21 0.7798408488063661
macro_precision_class22 0.5658561146817522
macro_precision_class23 0.884326082537101
macro_precision_class24 0.7387470997679815
macro_precision_class25 0.0
macro_precision_class26 0.0
macro_precision_class27 0.0
macro_precision_class28 0.5594526768642447
macro_precision_class29 0.0
macro_precision_class30 0.0
macro_precision_class31 0.0
macro_precision_class32 0.0
macro_precision_class33 0.0
macro_precision_class34 0.5
macro_precision_class35 0.0
macro_precision_class36 0.0
macro_precision_class37 0.0
macro_precision_class38 0.0
macro_precision_class39 0.7431838170624451
macro_precision_class40 0.0
macro_precision_class41 0.0
macro_precision_class42 0.9389683350357507
macro_recall_class0 0.0
macro_recall_class1 0.15829944077275038
macro_recall_class2 0.0
macro_recall_class3 0.0
macro_recall_class4 0.0
macro_recall_class5 0.0
macro_recall_class6 0.0
macro_recall_class7 0.0
macro_recall_class8 0.0
macro_recall_class9 0.0
macro_recall_class10 0.0
macro_recall_class11 0.5612985331219719
macro_recall_class12 0.0
macro_recall_class13 0.0
macro_recall_class14 0.0
macro_recall_class15 0.0
macro_recall_class16 0.0
macro_recall_class17 0.2515515101365329
macro_recall_class18 0.0
macro_recall_class19 0.3602465090880724
macro_recall_class20 0.42911518781979285
macro_recall_class21 0.08098058118716431
macro_recall_class22 0.5597714620568415
macro_recall_class23 0.5501871901244562
macro_recall_class24 0.560024389671912
macro_recall_class25 0.0
macro_recall_class26 0.0
macro_recall_class27 0.0
macro_recall_class28 0.2585678385021126
macro_recall_class29 0.0
macro_recall_class30 0.0
macro_recall_class31 0.0
macro_recall_class32 0.0
macro_recall_class33 0.0
macro_recall_class34 0.0002486943546381497
macro_recall_class35 0.0
macro_recall_class36 0.0
macro_recall_class37 0.0
macro_recall_class38 0.0
macro_recall_class39 0.060530085959885384
macro_recall_class40 0.0
macro_recall_class41 0.0
macro_recall_class42 0.8429136340191415
macro_f1_score_class0 0.0
macro_f1_score_class1 0.2494867043918073
macro_f1_score_class2 0.0
macro_f1_score_class3 0.0
macro_f1_score_class4 0.0
macro_f1_score_class5 0.0
macro_f1_score_class6 0.0
macro_f1_score_class7 0.0
macro_f1_score_class8 0.0
macro_f1_score_class9 0.0
macro_f1_score_class10 0.0
macro_f1_score_class11 0.6251554185109663
macro_f1_score_class12 0.0
macro_f1_score_class13 0.0
macro_f1_score_class14 0.0
macro_f1_score_class15 0.0
macro_f1_score_class16 0.0
macro_f1_score_class17 0.3776162971244022
macro_f1_score_class18 0.0
macro_f1_score_class19 0.4105892551512592
macro_f1_score_class20 0.4397832099633887
macro_f1_score_class21 0.1467248908296943
macro_f1_score_class22 0.5627973428777635
macro_f1_score_class23 0.6783414162521247
macro_f1_score_class24 0.6370888135954966
macro_f1_score_class25 0.0
macro_f1_score_class26 0.0
macro_f1_score_class27 0.0
macro_f1_score_class28 0.35367442914612723
macro_f1_score_class29 0.0
macro_f1_score_class30 0.0
macro_f1_score_class31 0.0
macro_f1_score_class32 0.0
macro_f1_score_class33 0.0
macro_f1_score_class34 0.0004971414367387521
macro_f1_score_class35 0.0
macro_f1_score_class36 0.0
macro_f1_score_class37 0.0
macro_f1_score_class38 0.0
macro_f1_score_class39 0.11194277008677221
macro_f1_score_class40 0.0
macro_f1_score_class41 0.0
macro_f1_score_class42 0.8883520067647148
macro_f1_score_class_avg 0.12748952781700595
macro_f2_score_class0 0.0
macro_f2_score_class1 0.18540571921937568
macro_f2_score_class2 0.0
macro_f2_score_class3 0.0
macro_f2_score_class4 0.0
macro_f2_score_class5 0.0
macro_f2_score_class6 0.0
macro_f2_score_class7 0.0
macro_f2_score_class8 0.0
macro_f2_score_class9 0.0
macro_f2_score_class10 0.0
macro_f2_score_class11 0.5852091529132428
macro_f2_score_class12 0.0
macro_f2_score_class13 0.0
macro_f2_score_class14 0.0
macro_f2_score_class15 0.0
macro_f2_score_class16 0.0
macro_f2_score_class17 0.2903201161280464
macro_f2_score_class18 0.0
macro_f2_score_class19 0.3788257877164631
macro_f2_score_class20 0.43331968116946534
macro_f2_score_class21 0.09866433988858313
macro_f2_score_class22 0.5609779014933963
macro_f2_score_class23 0.5951631704820959
macro_f2_score_class24 0.5884991300661938
macro_f2_score_class25 0.0
macro_f2_score_class26 0.0
macro_f2_score_class27 0.0
macro_f2_score_class28 0.2897326401782399
macro_f2_score_class29 0.0
macro_f2_score_class30 0.0
macro_f2_score_class31 0.0
macro_f2_score_class32 0.0
macro_f2_score_class33 0.0
macro_f2_score_class34 0.00031082929255253015
macro_f2_score_class35 0.0
macro_f2_score_class36 0.0
macro_f2_score_class37 0.0
macro_f2_score_class38 0.0
macro_f2_score_class39 0.07415272829387297
macro_f2_score_class40 0.0
macro_f2_score_class41 0.0
macro_f2_score_class42 0.860519541305874
macro_f2_score_class_avg 0.11490931949180005
macro_f0_5_score_class0 0.0
macro_f0_5_score_class1 0.38125994857352763
macro_f0_5_score_class2 0.0
macro_f0_5_score_class3 0.0
macro_f0_5_score_class4 0.0
macro_f0_5_score_class5 0.0
macro_f0_5_score_class6 0.0
macro_f0_5_score_class7 0.0
macro_f0_5_score_class8 0.0
macro_f0_5_score_class9 0.0
macro_f0_5_score_class10 0.0
macro_f0_5_score_class11 0.6709546558488351
macro_f0_5_score_class12 0.0
macro_f0_5_score_class13 0.0
macro_f0_5_score_class14 0.0
macro_f0_5_score_class15 0.0
macro_f0_5_score_class16 0.0
macro_f0_5_score_class17 0.5399836583892856
macro_f0_5_score_class18 0.0
macro_f0_5_score_class19 0.4481667669493993
macro_f0_5_score_class20 0.4464424824720852
macro_f0_5_score_class21 0.28607570302617497
macro_f0_5_score_class22 0.5646286247945999
macro_f0_5_score_class23 0.7885463193920761
macro_f0_5_score_class24 0.6944242501788405
macro_f0_5_score_class25 0.0
macro_f0_5_score_class26 0.0
macro_f0_5_score_class27 0.0
macro_f0_5_score_class28 0.45383161262178273
macro_f0_5_score_class29 0.0
macro_f0_5_score_class30 0.0
macro_f0_5_score_class31 0.0
macro_f0_5_score_class32 0.0
macro_f0_5_score_class33 0.0
macro_f0_5_score_class34 0.0012410027302060065
macro_f0_5_score_class35 0.0
macro_f0_5_score_class36 0.0
macro_f0_5_score_class37 0.0
macro_f0_5_score_class38 0.0
macro_f0_5_score_class39 0.22827966284849796
macro_f0_5_score_class40 0.0
macro_f0_5_score_class41 0.0
macro_f0_5_score_class42 0.9180450658510705
macro_f0_5_score_class_avg 0.14934604078317165
macro_accuracy_class0 0.0
macro_accuracy_class1 0.1425220276919556
macro_accuracy_class2 0.0
macro_accuracy_class3 0.0
macro_accuracy_class4 0.0
macro_accuracy_class5 0.0
macro_accuracy_class6 0.0
macro_accuracy_class7 0.0
macro_accuracy_class8 0.0
macro_accuracy_class9 0.0
macro_accuracy_class10 0.0
macro_accuracy_class11 0.45470988279554336
macro_accuracy_class12 0.0
macro_accuracy_class13 0.0
macro_accuracy_class14 0.0
macro_accuracy_class15 0.0
macro_accuracy_class16 0.0
macro_accuracy_class17 0.2327540004593829
macro_accuracy_class18 0.0
macro_accuracy_class19 0.258327972477834
macro_accuracy_class20 0.28187314274003483
macro_accuracy_class21 0.07917059377945335
macro_accuracy_class22 0.39159219480200047
macro_accuracy_class23 0.513250112089105
macro_accuracy_class24 0.4674470500724269
macro_accuracy_class25 0.0
macro_accuracy_class26 0.0
macro_accuracy_class27 0.0
macro_accuracy_class28 0.21482654185022027
macro_accuracy_class29 0.0
macro_accuracy_class30 0.0
macro_accuracy_class31 0.0
macro_accuracy_class32 0.0
macro_accuracy_class33 0.0
macro_accuracy_class34 0.0002486325211337643
macro_accuracy_class35 0.0
macro_accuracy_class36 0.0
macro_accuracy_class37 0.0
macro_accuracy_class38 0.0
macro_accuracy_class39 0.059289924221161944
macro_accuracy_class40 0.0
macro_accuracy_class41 0.0
macro_accuracy_class42 0.7991306710133116

S-CNN-All (1)

sample_precision 0.7256559
sample_recall 0.56008214
sample_f1_score 0.5994399
sample_f2_score 0.56998324
sample_f0_5_score 0.65506876
hamming_loss 0.047741625
subset_accuracy 0.21819237
sample_accuracy 0.5001907
one_error 0.18712758
coverage 9.190179
label_cardinality 2.9340966
ranking_loss 0.06393432
label_ranking_avg_precision 0.76417387
micro_precision 0.7296983854800898
macro_precision_class_avg 0.43038259009142693
micro_recall 0.4770445948178054
macro_recall_class_avg 0.16698888322924807
micro_f1_score 0.5769226360937686
micro_f2_score 0.512537194977907
micro_f0_5_score 0.659808440053062
micro_accuracy 0.40540497005037246
macro_accuracy_class_avg 0.1445425828814164
macro_precision_class0 0.7011363636363637
macro_precision_class1 0.6375771058752149
macro_precision_class2 0.6586345381526104
macro_precision_class3 0.0
macro_precision_class4 0.0
macro_precision_class5 0.0
macro_precision_class6 0.0
macro_precision_class7 0.0
macro_precision_class8 0.0
macro_precision_class9 0.3333333333333333
macro_precision_class10 0.0
macro_precision_class11 0.8258865958725706
macro_precision_class12 0.24390243902439024
macro_precision_class13 0.0
macro_precision_class14 0.2222222222222222
macro_precision_class15 0.0
macro_precision_class16 0.4962962962962963
macro_precision_class17 0.7374895343595028
macro_precision_class18 0.4482758620689655
macro_precision_class19 0.6065450441978559
macro_precision_class20 0.5587703435804702
macro_precision_class21 0.7571080422420796
macro_precision_class22 0.7163413898582569
macro_precision_class23 0.807987751076971
macro_precision_class24 0.75508279477436
macro_precision_class25 0.5354609929078015
macro_precision_class26 0.0
macro_precision_class27 0.5251798561151079
macro_precision_class28 0.5628059341817908
macro_precision_class29 0.9
macro_precision_class30 0.3333333333333333
macro_precision_class31 0.0
macro_precision_class32 0.0
macro_precision_class33 1.0
macro_precision_class34 0.8627787307032591
macro_precision_class35 0.0
macro_precision_class36 0.0
macro_precision_class37 0.0
macro_precision_class38 0.8
macro_precision_class39 0.7615321252059308
macro_precision_class40 0.8028169014084507
macro_precision_class41 1.0
macro_precision_class42 0.9159538435042195
macro_recall_class0 0.2336236274138584
macro_recall_class1 0.4006736146415862
macro_recall_class2 0.0584045584045584
macro_recall_class3 0.0
macro_recall_class4 0.0
macro_recall_class5 0.0
macro_recall_class6 0.0
macro_recall_class7 0.0
macro_recall_class8 0.0
macro_recall_class9 0.006896551724137931
macro_recall_class10 0.0
macro_recall_class11 0.5521891535868182
macro_recall_class12 0.0030284675953967293
macro_recall_class13 0.0
macro_recall_class14 0.0017777777777777779
macro_recall_class15 0.0
macro_recall_class16 0.021909744931327666
macro_recall_class17 0.4737691352916839
macro_recall_class18 0.007449856733524355
macro_recall_class19 0.25157968640299555
macro_recall_class20 0.34706102583302134
macro_recall_class21 0.38507092686957717
macro_recall_class22 0.4871667154995605
macro_recall_class23 0.7875898006678134
macro_recall_class24 0.6384231139044582
macro_recall_class25 0.05644859813084112
macro_recall_class26 0.0
macro_recall_class27 0.05421463052357965
macro_recall_class28 0.4127751235812322
macro_recall_class29 0.04072398190045249
macro_recall_class30 0.018018018018018018
macro_recall_class31 0.0
macro_recall_class32 0.0
macro_recall_class33 0.0029585798816568047
macro_recall_class34 0.12509326038298932
macro_recall_class35 0.0
macro_recall_class36 0.0
macro_recall_class37 0.0
macro_recall_class38 0.00516795865633075
macro_recall_class39 0.39734957020057304
macro_recall_class40 0.49279538904899134
macro_recall_class41 0.00398406374501992
macro_recall_class42 0.914379047509886
macro_f1_score_class0 0.3504686168702073
macro_f1_score_class1 0.49209756097560975
macro_f1_score_class2 0.10729473339875695
macro_f1_score_class3 0.0
macro_f1_score_class4 0.0
macro_f1_score_class5 0.0
macro_f1_score_class6 0.0
macro_f1_score_class7 0.0
macro_f1_score_class8 0.0
macro_f1_score_class9 0.013513513513513514
macro_f1_score_class10 0.0
macro_f1_score_class11 0.661858566936509
macro_f1_score_class12 0.005982650314089142
macro_f1_score_class13 0.0
macro_f1_score_class14 0.003527336860670194
macro_f1_score_class15 0.0
macro_f1_score_class16 0.041966802380206704
macro_f1_score_class17 0.5769201702899464
macro_f1_score_class18 0.014656144306651634
macro_f1_score_class19 0.35564622849580946
macro_f1_score_class20 0.428175519630485
macro_f1_score_class21 0.5104984480555048
macro_f1_score_class22 0.5799340785825151
macro_f1_score_class23 0.7976583916173494
macro_f1_score_class24 0.6918697740615549
macro_f1_score_class25 0.10213053770713561
macro_f1_score_class26 0.0
macro_f1_score_class27 0.09828340626051835
macro_f1_score_class28 0.47625420191495804
macro_f1_score_class29 0.07792207792207792
macro_f1_score_class30 0.03418803418803419
macro_f1_score_class31 0.0
macro_f1_score_class32 0.0
macro_f1_score_class33 0.0058997050147492625
macro_f1_score_class34 0.21850564726324934
macro_f1_score_class35 0.0
macro_f1_score_class36 0.0
macro_f1_score_class37 0.0
macro_f1_score_class38 0.010269576379974325
macro_f1_score_class39 0.5222180380342685
macro_f1_score_class40 0.6107142857142858
macro_f1_score_class41 0.007936507936507936
macro_f1_score_class42 0.9151657680394631
macro_f1_score_class_avg 0.20259433308522332
macro_f2_score_class0 0.2695735756728417
macro_f2_score_class1 0.43283950956297285
macro_f2_score_class2 0.07142234996951485
macro_f2_score_class3 0.0
macro_f2_score_class4 0.0
macro_f2_score_class5 0.0
macro_f2_score_class6 0.0
macro_f2_score_class7 0.0
macro_f2_score_class8 0.0
macro_f2_score_class9 0.008576329331046312
macro_f2_score_class10 0.0
macro_f2_score_class11 0.591386022132739
macro_f2_score_class12 0.0037738697260170577
macro_f2_score_class13 0.0
macro_f2_score_class14 0.0022177866489243737
macro_f2_score_class15 0.0
macro_f2_score_class16 0.027088218646397672
macro_f2_score_class17 0.5102622831017672
macro_f2_score_class18 0.009273790840348123
macro_f2_score_class19 0.2849292315304013
macro_f2_score_class20 0.37551648707769586
macro_f2_score_class21 0.4270397409658796
macro_f2_score_class22 0.5204687881500774
macro_f2_score_class23 0.7915865800205428
macro_f2_score_class24 0.6587792872803303
macro_f2_score_class25 0.06874886177381169
macro_f2_score_class26 0.0
macro_f2_score_class27 0.0660633484162896
macro_f2_score_class28 0.43602175003792254
macro_f2_score_class29 0.050335570469798654
macro_f2_score_class30 0.022222222222222223
macro_f2_score_class31 0.0
macro_f2_score_class32 0.0
macro_f2_score_class33 0.003695491500369549
macro_f2_score_class34 0.1508969820603588
macro_f2_score_class35 0.0
macro_f2_score_class36 0.0
macro_f2_score_class37 0.0
macro_f2_score_class38 0.0064495324089003546
macro_f2_score_class39 0.4393732970027248
macro_f2_score_class40 0.5340412242348532
macro_f2_score_class41 0.004975124378109453
macro_f2_score_class42 0.9146935733532076
macro_f2_score_class_avg 0.17865699601200152
macro_f0_5_score_class0 0.5007304009089434
macro_f0_5_score_class1 0.570154814439702
macro_f0_5_score_class2 0.21556256572029442
macro_f0_5_score_class3 0.0
macro_f0_5_score_class4 0.0
macro_f0_5_score_class5 0.0
macro_f0_5_score_class6 0.0
macro_f0_5_score_class7 0.0
macro_f0_5_score_class8 0.0
macro_f0_5_score_class9 0.03184713375796178
macro_f0_5_score_class10 0.0
macro_f0_5_score_class11 0.7513990751824419
macro_f0_5_score_class12 0.014425851125216388
macro_f0_5_score_class13 0.0
macro_f0_5_score_class14 0.008613264427217916
macro_f0_5_score_class15 0.0
macro_f0_5_score_class16 0.09310728182323513
macro_f0_5_score_class17 0.6636106539326364
macro_f0_5_score_class18 0.03492745835572273
macro_f0_5_score_class19 0.47305424355326076
macro_f0_5_score_class20 0.49801224884495543
macro_f0_5_score_class21 0.634502791267644
macro_f0_5_score_class22 0.6547403405421582
macro_f0_5_score_class23 0.8038240700580375
macro_f0_5_score_class24 0.7284603404815551
macro_f0_5_score_class25 0.19852747830660006
macro_f0_5_score_class26 0.0
macro_f0_5_score_class27 0.19185282522996058
macro_f0_5_score_class28 0.5246660067535787
macro_f0_5_score_class29 0.1724137931034483
macro_f0_5_score_class30 0.07407407407407407
macro_f0_5_score_class31 0.0
macro_f0_5_score_class32 0.0
macro_f0_5_score_class33 0.014619883040935672
macro_f0_5_score_class34 0.39587596411144343
macro_f0_5_score_class35 0.0
macro_f0_5_score_class36 0.0
macro_f0_5_score_class37 0.0
macro_f0_5_score_class38 0.02518891687657431
macro_f0_5_score_class39 0.6435632077222944
macro_f0_5_score_class40 0.713094245204337
macro_f0_5_score_class41 0.0196078431372549
macro_f0_5_score_class42 0.9156384505021521
macro_f0_5_score_class_avg 0.24572314470892176
macro_accuracy_class0 0.212465564738292
macro_accuracy_class1 0.32634575569358176
macro_accuracy_class2 0.056688558589699276
macro_accuracy_class3 0.0
macro_accuracy_class4 0.0
macro_accuracy_class5 0.0
macro_accuracy_class6 0.0
macro_accuracy_class7 0.0
macro_accuracy_class8 0.0
macro_accuracy_class9 0.006802721088435374
macro_accuracy_class10 0.0
macro_accuracy_class11 0.4946103233805972
macro_accuracy_class12 0.003000300030003
macro_accuracy_class13 0.0
macro_accuracy_class14 0.0017667844522968198
macro_accuracy_class15 0.0
macro_accuracy_class16 0.02143314139475368
macro_accuracy_class17 0.4054025348721943
macro_accuracy_class18 0.0073821692220329355
macro_accuracy_class19 0.21628328079940984
macro_accuracy_class20 0.2724066999706142
macro_accuracy_class21 0.34273106153468985
macro_accuracy_class22 0.4083853220022597
macro_accuracy_class23 0.6634207666574332
macro_accuracy_class24 0.5288997688018496
macro_accuracy_class25 0.05381325730577334
macro_accuracy_class26 0.0
macro_accuracy_class27 0.05168141592920354
macro_accuracy_class28 0.31255489105432643
macro_accuracy_class29 0.04054054054054054
macro_accuracy_class30 0.017391304347826087
macro_accuracy_class31 0.0
macro_accuracy_class32 0.0
macro_accuracy_class33 0.0029585798816568047
macro_accuracy_class34 0.12265301146061935
macro_accuracy_class35 0.0
macro_accuracy_class36 0.0
macro_accuracy_class37 0.0
macro_accuracy_class38 0.005161290322580645
macro_accuracy_class39 0.35337962668025735
macro_accuracy_class40 0.43958868894601544
macro_accuracy_class41 0.00398406374501992
macro_accuracy_class42 0.8435996404589435

S-CNN-All (2)

sample_precision 0.74113655
sample_recall 0.53892523
sample_f1_score 0.58889484
sample_f2_score 0.55281276
sample_f0_5_score 0.6552151
hamming_loss 0.047080718
subset_accuracy 0.22032161
sample_accuracy 0.49142346
one_error 0.17301734
coverage 13.15306
label_cardinality 2.9340966
ranking_loss 0.09842518
label_ranking_avg_precision 0.75966877
micro_precision 0.7639075213793421
macro_precision_class_avg 0.4295293735644244
micro_recall 0.4486911831206354
macro_recall_class_avg 0.15235841129842634
micro_f1_score 0.5653289390636248
micro_f2_score 0.48905129308487505
micro_f0_5_score 0.6697977223247583
micro_accuracy 0.39404777475238695
macro_accuracy_class_avg 0.13678938567465415
macro_precision_class0 0.7200811359026369
macro_precision_class1 0.7240013774104683
macro_precision_class2 0.6869565217391305
macro_precision_class3 0.0
macro_precision_class4 0.0
macro_precision_class5 0.0
macro_precision_class6 0.0
macro_precision_class7 0.0
macro_precision_class8 0.0
macro_precision_class9 0.0
macro_precision_class10 0.0
macro_precision_class11 0.7625684420109508
macro_precision_class12 0.31645569620253167
macro_precision_class13 0.0
macro_precision_class14 1.0
macro_precision_class15 0.0
macro_precision_class16 0.3894736842105263
macro_precision_class17 0.8076860578485537
macro_precision_class18 0.3
macro_precision_class19 0.6205948258301079
macro_precision_class20 0.6073942009946514
macro_precision_class21 0.7242016363156506
macro_precision_class22 0.7662873829423019
macro_precision_class23 0.8651216758655293
macro_precision_class24 0.7676296727999334
macro_precision_class25 0.7333333333333333
macro_precision_class26 0.0
macro_precision_class27 0.5188679245283019
macro_precision_class28 0.572897283215482
macro_precision_class29 0.5
macro_precision_class30 0.3333333333333333
macro_precision_class31 0.0
macro_precision_class32 0.0
macro_precision_class33 1.0
macro_precision_class34 0.8222222222222222
macro_precision_class35 0.0
macro_precision_class36 0.0
macro_precision_class37 0.0
macro_precision_class38 0.5
macro_precision_class39 0.8210658132739669
macro_precision_class40 0.9184782608695652
macro_precision_class41 0.7142857142857143
macro_precision_class42 0.9768268681353565
macro_recall_class0 0.1344187807648618
macro_recall_class1 0.2672216573462125
macro_recall_class2 0.028133903133903133
macro_recall_class3 0.0
macro_recall_class4 0.0
macro_recall_class5 0.0
macro_recall_class6 0.0
macro_recall_class7 0.0
macro_recall_class8 0.0
macro_recall_class9 0.0
macro_recall_class10 0.0
macro_recall_class11 0.6840965415615441
macro_recall_class12 0.007571168988491823
macro_recall_class13 0.0
macro_recall_class14 0.0008888888888888889
macro_recall_class15 0.0
macro_recall_class16 0.02419882275997384
macro_recall_class17 0.41129499379395945
macro_recall_class18 0.0017191977077363897
macro_recall_class19 0.23765504329510884
macro_recall_class20 0.20195307625109196
macro_recall_class21 0.3779093788734334
macro_recall_class22 0.4459419865221213
macro_recall_class23 0.6978397247799252
macro_recall_class24 0.6486714664290237
macro_recall_class25 0.00822429906542056
macro_recall_class26 0.0
macro_recall_class27 0.020423319717786857
macro_recall_class28 0.340090027892077
macro_recall_class29 0.004524886877828055
macro_recall_class30 0.01126126126126126
macro_recall_class31 0.0
macro_recall_class32 0.0
macro_recall_class33 0.0014792899408284023
macro_recall_class34 0.1840338224322308
macro_recall_class35 0.0
macro_recall_class36 0.0
macro_recall_class37 0.0
macro_recall_class38 0.0004306632213608958
macro_recall_class39 0.42270773638968484
macro_recall_class40 0.48703170028818443
macro_recall_class41 0.0199203187250996
macro_recall_class42 0.8817697289242936
macro_f1_score_class0 0.2265475430759413
macro_f1_score_class1 0.3903639064240624
macro_f1_score_class2 0.05405405405405406
macro_f1_score_class3 0.0
macro_f1_score_class4 0.0
macro_f1_score_class5 0.0
macro_f1_score_class6 0.0
macro_f1_score_class7 0.0
macro_f1_score_class8 0.0
macro_f1_score_class9 0.0
macro_f1_score_class10 0.0
macro_f1_score_class11 0.7212042038861232
macro_f1_score_class12 0.014788524105294291
macro_f1_score_class13 0.0
macro_f1_score_class14 0.0017761989342806395
macro_f1_score_class15 0.0
macro_f1_score_class16 0.04556650246305419
macro_f1_score_class17 0.5450408465376392
macro_f1_score_class18 0.003418803418803419
macro_f1_score_class19 0.34369359205776173
macro_f1_score_class20 0.30312112201175395
macro_f1_score_class21 0.49665158371040724
macro_f1_score_class22 0.5637872277374426
macro_f1_score_class23 0.7725287034444134
macro_f1_score_class24 0.7031547906551084
macro_f1_score_class25 0.016266173752310535
macro_f1_score_class26 0.0
macro_f1_score_class27 0.039299749910682386
macro_f1_score_class28 0.4268113053875613
macro_f1_score_class29 0.008968609865470852
macro_f1_score_class30 0.02178649237472767
macro_f1_score_class31 0.0
macro_f1_score_class32 0.0
macro_f1_score_class33 0.0029542097488921715
macro_f1_score_class34 0.3007518796992481
macro_f1_score_class35 0.0
macro_f1_score_class36 0.0
macro_f1_score_class37 0.0
macro_f1_score_class38 0.0008605851979345956
macro_f1_score_class39 0.5580933465739821
macro_f1_score_class40 0.6365348399246704
macro_f1_score_class41 0.03875968992248062
macro_f1_score_class42 0.9268674698795181
macro_f1_score_class_avg 0.18985237104078181
macro_f2_score_class0 0.1605317898164059
macro_f2_score_class1 0.3058092855480568
macro_f2_score_class2 0.03481096325019829
macro_f2_score_class3 0.0
macro_f2_score_class4 0.0
macro_f2_score_class5 0.0
macro_f2_score_class6 0.0
macro_f2_score_class7 0.0
macro_f2_score_class8 0.0
macro_f2_score_class9 0.0
macro_f2_score_class10 0.0
macro_f2_score_class11 0.69847175110333
macro_f2_score_class12 0.009407691728757432
macro_f2_score_class13 0.0
macro_f2_score_class14 0.001110864252388358
macro_f2_score_class15 0.0
macro_f2_score_class16 0.02978586379004991
macro_f2_score_class17 0.4560593826843322
macro_f2_score_class18 0.002145922746781116
macro_f2_score_class19 0.27111328646435884
macro_f2_score_class20 0.2330680877110863
macro_f2_score_class21 0.4178722626625651
macro_f2_score_class22 0.4866288959087363
macro_f2_score_class23 0.725912554732233
macro_f2_score_class24 0.6694192074425088
macro_f2_score_class25 0.010251630941286114
macro_f2_score_class26 0.0
macro_f2_score_class27 0.025280382423239565
macro_f2_score_class28 0.3701755440663701
macro_f2_score_class29 0.0056433408577878106
macro_f2_score_class30 0.013958682300390842
macro_f2_score_class31 0.0
macro_f2_score_class32 0.0
macro_f2_score_class33 0.0018484288354898336
macro_f2_score_class34 0.21785209609043807
macro_f2_score_class35 0.0
macro_f2_score_class36 0.0
macro_f2_score_class37 0.0
macro_f2_score_class38 0.0005382131324004305
macro_f2_score_class39 0.4681327050311771
macro_f2_score_class40 0.537531806615776
macro_f2_score_class41 0.024727992087042534
macro_f2_score_class42 0.8992717453563538
macro_f2_score_class_avg 0.16458977622277998
macro_f0_5_score_class0 0.38478213743767614
macro_f0_5_score_class1 0.5395452679121331
macro_f0_5_score_class2 0.12086903304773562
macro_f0_5_score_class3 0.0
macro_f0_5_score_class4 0.0
macro_f0_5_score_class5 0.0
macro_f0_5_score_class6 0.0
macro_f0_5_score_class7 0.0
macro_f0_5_score_class8 0.0
macro_f0_5_score_class9 0.0
macro_f0_5_score_class10 0.0
macro_f0_5_score_class11 0.7454661353030767
macro_f0_5_score_class12 0.03454947484798231
macro_f0_5_score_class13 0.0
macro_f0_5_score_class14 0.0044286979627989375
macro_f0_5_score_class15 0.0
macro_f0_5_score_class16 0.09690937663698271
macro_f0_5_score_class17 0.6771613852483583
macro_f0_5_score_class18 0.008403361344537815
macro_f0_5_score_class19 0.46934216607610535
macro_f0_5_score_class20 0.43338243170862345
macro_f0_5_score_class21 0.6120355087656689
macro_f0_5_score_class22 0.6700241243902868
macro_f0_5_score_class23 0.82554284072682
macro_f0_5_score_class24 0.7404710530965396
macro_f0_5_score_class25 0.03935599284436494
macro_f0_5_score_class26 0.0
macro_f0_5_score_class27 0.08822585819698428
macro_f0_5_score_class28 0.5039076885306273
macro_f0_5_score_class29 0.021834061135371178
macro_f0_5_score_class30 0.0496031746031746
macro_f0_5_score_class31 0.0
macro_f0_5_score_class32 0.0
macro_f0_5_score_class33 0.007352941176470588
macro_f0_5_score_class34 0.4855005904736911
macro_f0_5_score_class35 0.0
macro_f0_5_score_class36 0.0
macro_f0_5_score_class37 0.0
macro_f0_5_score_class38 0.002145922746781116
macro_f0_5_score_class39 0.6908541725203708
macro_f0_5_score_class40 0.7802400738688827
macro_f0_5_score_class41 0.08960573476702509
macro_f0_5_score_class42 0.9562104582799895
macro_f0_5_score_class_avg 0.23436627124765252
macro_accuracy_class0 0.12774379273119826
macro_accuracy_class1 0.24251686948497606
macro_accuracy_class2 0.027777777777777776
macro_accuracy_class3 0.0
macro_accuracy_class4 0.0
macro_accuracy_class5 0.0
macro_accuracy_class6 0.0
macro_accuracy_class7 0.0
macro_accuracy_class8 0.0
macro_accuracy_class9 0.0
macro_accuracy_class10 0.0
macro_accuracy_class11 0.5639713596789928
macro_accuracy_class12 0.007449344457687724
macro_accuracy_class13 0.0
macro_accuracy_class14 0.0008888888888888889
macro_accuracy_class15 0.0
macro_accuracy_class16 0.023314429741650915
macro_accuracy_class17 0.37460903643968796
macro_accuracy_class18 0.0017123287671232876
macro_accuracy_class19 0.20750604502264755
macro_accuracy_class20 0.17863450711999118
macro_accuracy_class21 0.3303635925836744
macro_accuracy_class22 0.392551325698958
macro_accuracy_class23 0.6293660027832911
macro_accuracy_class24 0.5422041008350649
macro_accuracy_class25 0.008199776369735371
macro_accuracy_class26 0.0
macro_accuracy_class27 0.020043731778425656
macro_accuracy_class28 0.27130331335918223
macro_accuracy_class29 0.0045045045045045045
macro_accuracy_class30 0.011013215859030838
macro_accuracy_class31 0.0
macro_accuracy_class32 0.0
macro_accuracy_class33 0.0014792899408284023
macro_accuracy_class34 0.17699115044247787
macro_accuracy_class35 0.0
macro_accuracy_class36 0.0
macro_accuracy_class37 0.0
macro_accuracy_class38 0.00043047783039173483
macro_accuracy_class39 0.38705234159779617
macro_accuracy_class40 0.46685082872928174
macro_accuracy_class41 0.019762845849802372
macro_accuracy_class42 0.8637027057370608

K-BranchCNN

sample_precision 0.76506287
sample_recall 0.7700607
sample_f1_score 0.74348545
sample_f2_score 0.75300294
sample_f0_5_score 0.74968976
hamming_loss 0.038586635
subset_accuracy 0.27576151
sample_accuracy 0.6406048
one_error 0.08616306
coverage 5.318831
label_cardinality 2.9340966
ranking_loss 0.025627982
label_ranking_avg_precision 0.8562701
micro_precision 0.7166083061779325
macro_precision_class_avg 0.5134869099363687
micro_recall 0.718735022461231
macro_recall_class_avg 0.3816313172874065
micro_f1_score 0.7176700887654759
micro_f2_score 0.7183086699527119
micro_f0_5_score 0.7170326419756021
micro_accuracy 0.5596610376767713
macro_accuracy_class_avg 0.2909035624492094
macro_precision_class0 0.6519060454370428
macro_precision_class1 0.6688840054641563
macro_precision_class2 0.5159203980099503
macro_precision_class3 0.0898876404494382
macro_precision_class4 0.4444444444444444
macro_precision_class5 0.0
macro_precision_class6 0.4230769230769231
macro_precision_class7 1.0
macro_precision_class8 0.0
macro_precision_class9 0.25552825552825553
macro_precision_class10 0.2857142857142857
macro_precision_class11 0.8263931705003557
macro_precision_class12 0.6973803071364046
macro_precision_class13 0.340956340956341
macro_precision_class14 0.3992673992673993
macro_precision_class15 0.371875
macro_precision_class16 0.4531129900076864
macro_precision_class17 0.7381362291970158
macro_precision_class18 0.44830741079597436
macro_precision_class19 0.5933487297921478
macro_precision_class20 0.6294776963080203
macro_precision_class21 0.6732894427462968
macro_precision_class22 0.6712658510559099
macro_precision_class23 0.8521456905878111
macro_precision_class24 0.7753879723020061
macro_precision_class25 0.4657596371882086
macro_precision_class26 0.43661971830985913
macro_precision_class27 0.5389244076720572
macro_precision_class28 0.5990045226003688
macro_precision_class29 0.4219858156028369
macro_precision_class30 0.3917963224893918
macro_precision_class31 0.0
macro_precision_class32 0.0
macro_precision_class33 0.4635416666666667
macro_precision_class34 0.6873828856964397
macro_precision_class35 0.37362637362637363
macro_precision_class36 0.6923076923076923
macro_precision_class37 0.3877551020408163
macro_precision_class38 0.6501623376623377
macro_precision_class39 0.798180439727066
macro_precision_class40 0.8571428571428571
macro_precision_class41 0.5352112676056338
macro_precision_class42 0.9748298521473833
macro_recall_class0 0.6410450586898901
macro_recall_class1 0.715683782409761
macro_recall_class2 0.3693019943019943
macro_recall_class3 0.03137254901960784
macro_recall_class4 0.05
macro_recall_class5 0.0
macro_recall_class6 0.02301255230125523
macro_recall_class7 0.012048192771084338
macro_recall_class8 0.0
macro_recall_class9 0.3586206896551724
macro_recall_class10 0.033112582781456956
macro_recall_class11 0.7780705083837549
macro_recall_class12 0.2337976983646275
macro_recall_class13 0.2519201228878648
macro_recall_class14 0.04844444444444444
macro_recall_class15 0.16951566951566951
macro_recall_class16 0.3855461085676913
macro_recall_class17 0.6918080264791063
macro_recall_class18 0.2808022922636103
macro_recall_class19 0.7515796864029956
macro_recall_class20 0.6080119805316361
macro_recall_class21 0.7887343341137585
macro_recall_class22 0.7925578669791972
macro_recall_class23 0.8368410401699888
macro_recall_class24 0.816702234938205
macro_recall_class25 0.3839252336448598
macro_recall_class26 0.3157894736842105
macro_recall_class27 0.532120311919792
macro_recall_class28 0.6547181795586976
macro_recall_class29 0.5384615384615384
macro_recall_class30 0.6238738738738738
macro_recall_class31 0.0
macro_recall_class32 0.0
macro_recall_class33 0.06582840236686391
macro_recall_class34 0.5473762745585675
macro_recall_class35 0.13127413127413126
macro_recall_class36 0.08571428571428572
macro_recall_class37 0.2275449101796407
macro_recall_class38 0.3449612403100775
macro_recall_class39 0.7541547277936963
macro_recall_class40 0.4322766570605187
macro_recall_class41 0.15139442231075698
macro_recall_class42 0.9522035646741933
macro_f1_score_class0 0.6464299350897289
macro_f1_score_class1 0.6914929542872932
macro_f1_score_class2 0.4304690743046907
macro_f1_score_class3 0.046511627906976744
macro_f1_score_class4 0.0898876404494382
macro_f1_score_class5 0.0
macro_f1_score_class6 0.04365079365079365
macro_f1_score_class7 0.023809523809523808
macro_f1_score_class8 0.0
macro_f1_score_class9 0.2984218077474892
macro_f1_score_class10 0.05934718100890208
macro_f1_score_class11 0.8015041571315218
macro_f1_score_class12 0.35019278748015426
macro_f1_score_class13 0.28975265017667845
macro_f1_score_class14 0.08640507332540626
macro_f1_score_class15 0.2328767123287671
macro_f1_score_class16 0.4166077738515901
macro_f1_score_class17 0.7142216432095337
macro_f1_score_class18 0.3453136011275546
macro_f1_score_class19 0.6631562645191266
macro_f1_score_class20 0.618558664360191
macro_f1_score_class21 0.7264539861736539
macro_f1_score_class22 0.7268867480887312
macro_f1_score_class23 0.8444240242999719
macro_f1_score_class24 0.7955090572675149
macro_f1_score_class25 0.4209016393442623
macro_f1_score_class26 0.3665024630541872
macro_f1_score_class27 0.5355007473841554
macro_f1_score_class28 0.625623433170603
macro_f1_score_class29 0.4731610337972167
macro_f1_score_class30 0.48132059079061684
macro_f1_score_class31 0.0
macro_f1_score_class32 0.0
macro_f1_score_class33 0.11528497409326424
macro_f1_score_class34 0.6094420600858369
macro_f1_score_class35 0.19428571428571428
macro_f1_score_class36 0.15254237288135594
macro_f1_score_class37 0.28679245283018867
macro_f1_score_class38 0.45075970737197524
macro_f1_score_class39 0.7755432780847146
macro_f1_score_class40 0.5747126436781609
macro_f1_score_class41 0.2360248447204969
macro_f1_score_class42 0.9633838749891283
macro_f1_score_class_avg 0.40008524446877
macro_f2_score_class0 0.6431882075830104
macro_f2_score_class1 0.7058071470650907
macro_f2_score_class2 0.39155716659114936
macro_f2_score_class3 0.03606853020739405
macro_f2_score_class4 0.060790273556231005
macro_f2_score_class5 0.0
macro_f2_score_class6 0.028379772961816305
macro_f2_score_class7 0.015015015015015015
macro_f2_score_class8 0.0
macro_f2_score_class9 0.3318442884492661
macro_f2_score_class10 0.04022526146419952
macro_f2_score_class11 0.7872775905225775
macro_f2_score_class12 0.2696472231924555
macro_f2_score_class13 0.26580226904376014
macro_f2_score_class14 0.05877278119271002
macro_f2_score_class15 0.19021739130434784
macro_f2_score_class16 0.39739786975866254
macro_f2_score_class17 0.7006025156494851
macro_f2_score_class18 0.303480738263347
macro_f2_score_class19 0.7135239618742918
macro_f2_score_class20 0.612187199607959
macro_f2_score_class21 0.7625832223701731
macro_f2_score_class22 0.7649151382502813
macro_f2_score_class23 0.8398578319370399
macro_f2_score_class24 0.8080908867303703
macro_f2_score_class25 0.3979077876791941
macro_f2_score_class26 0.3342918763479511
macro_f2_score_class27 0.5334673516491698
macro_f2_score_class28 0.642761477689876
macro_f2_score_class29 0.5102915951972555
macro_f2_score_class30 0.5577929923479662
macro_f2_score_class31 0.0
macro_f2_score_class32 0.0
macro_f2_score_class33 0.07946428571428571
macro_f2_score_class34 0.570621175982578
macro_f2_score_class35 0.15084294587400177
macro_f2_score_class36 0.10392609699769054
macro_f2_score_class37 0.24804177545691905
macro_f2_score_class38 0.38070342205323193
macro_f2_score_class39 0.7625669998551354
macro_f2_score_class40 0.4798464491362764
macro_f2_score_class41 0.17674418604651163
macro_f2_score_class42 0.9566444035006909
macro_f2_score_class_avg 0.3863522582353341
macro_f0_5_score_class0 0.6497045053342544
macro_f0_5_score_class1 0.6777478214818737
macro_f0_5_score_class2 0.47796828908554573
macro_f0_5_score_class3 0.06546644844517185
macro_f0_5_score_class4 0.1724137931034483
macro_f0_5_score_class5 0.0
macro_f0_5_score_class6 0.09450171821305842
macro_f0_5_score_class7 0.05747126436781609
macro_f0_5_score_class8 0.0
macro_f0_5_score_class9 0.2711157455683003
macro_f0_5_score_class10 0.11312217194570136
macro_f0_5_score_class11 0.8162543507488207
macro_f0_5_score_class12 0.49935316946959896
macro_f0_5_score_class13 0.31844660194174756
macro_f0_5_score_class14 0.16307600239377618
macro_f0_5_score_class15 0.3002018163471241
macro_f0_5_score_class16 0.437769196494876
macro_f0_5_score_class17 0.7283807565646181
macro_f0_5_score_class18 0.4005231322543731
macro_f0_5_score_class19 0.61943062145585
macro_f0_5_score_class20 0.6250641486195218
macro_f0_5_score_class21 0.6935933147632312
macro_f0_5_score_class22 0.6924605003123112
macro_f0_5_score_class23 0.8490401396160558
macro_f0_5_score_class24 0.7833130148047847
macro_f0_5_score_class25 0.44671596346237497
macro_f0_5_score_class26 0.40558220671609246
macro_f0_5_score_class27 0.5375497036536875
macro_f0_5_score_class28 0.60937556226128
macro_f0_5_score_class29 0.44106745737583397
macro_f0_5_score_class30 0.4232885085574572
macro_f0_5_score_class31 0.0
macro_f0_5_score_class32 0.0
macro_f0_5_score_class33 0.2099056603773585
macro_f0_5_score_class34 0.6539307148374829
macro_f0_5_score_class35 0.27287319422150885
macro_f0_5_score_class36 0.28662420382165604
macro_f0_5_score_class37 0.33989266547406083
macro_f0_5_score_class38 0.5524137931034483
macro_f0_5_score_class39 0.7889688249400479
macro_f0_5_score_class40 0.7163323782234957
macro_f0_5_score_class41 0.35514018691588783
macro_f0_5_score_class42 0.9702189781021898
macro_f0_5_score_class_avg 0.43061159361338885
macro_accuracy_class0 0.477574047954866
macro_accuracy_class1 0.5284594810191919
macro_accuracy_class2 0.27426606717799523
macro_accuracy_class3 0.023809523809523808
macro_accuracy_class4 0.047058823529411764
macro_accuracy_class5 0.0
macro_accuracy_class6 0.02231237322515213
macro_accuracy_class7 0.012048192771084338
macro_accuracy_class8 0.0
macro_accuracy_class9 0.17537942664418213
macro_accuracy_class10 0.03058103975535168
macro_accuracy_class11 0.6687583957014008
macro_accuracy_class12 0.2122628540005499
macro_accuracy_class13 0.16942148760330578
macro_accuracy_class14 0.04515327257663629
macro_accuracy_class15 0.13178294573643412
macro_accuracy_class16 0.2631109127426914
macro_accuracy_class17 0.5554780413261577
macro_accuracy_class18 0.20868824531516184
macro_accuracy_class19 0.49606116774791476
macro_accuracy_class20 0.4477632516140891
macro_accuracy_class21 0.5704183266932271
macro_accuracy_class22 0.5709521497773181
macro_accuracy_class23 0.7307386464039582
macro_accuracy_class24 0.6604525024180242
macro_accuracy_class25 0.2665455489229172
macro_accuracy_class26 0.2243667068757539
macro_accuracy_class27 0.36565450369992347
macro_accuracy_class28 0.4552052532544833
macro_accuracy_class29 0.3098958333333333
macro_accuracy_class30 0.3169336384439359
macro_accuracy_class31 0.0
macro_accuracy_class32 0.0
macro_accuracy_class33 0.061168384879725084
macro_accuracy_class34 0.4382716049382716
macro_accuracy_class35 0.10759493670886076
macro_accuracy_class36 0.08256880733944955
macro_accuracy_class37 0.16740088105726872
macro_accuracy_class38 0.29095532146749004
macro_accuracy_class39 0.6333774515702082
macro_accuracy_class40 0.4032258064516129
macro_accuracy_class41 0.13380281690140844
macro_accuracy_class42 0.9293545139277324

ResNet50

sample_precision 0.7934544
sample_recall 0.7538527
sample_f1_score 0.74999356
sample_f2_score 0.7470173
sample_f0_5_score 0.7685211
hamming_loss 0.035113577
subset_accuracy 0.28909317
sample_accuracy 0.65052855
one_error 0.08640141
coverage 8.342666
label_cardinality 2.9340966
ranking_loss 0.046847016
label_ranking_avg_precision 0.8529595
micro_precision 0.7589474535726255
macro_precision_class_avg 0.6059247311583635
micro_recall 0.7113291795625813
macro_recall_class_avg 0.4917724422891552
micro_f1_score 0.7343672031700992
micro_f2_score 0.720368729104327
micro_f0_5_score 0.7489205060236704
micro_accuracy 0.580237178595173
macro_accuracy_class_avg 0.3896077251647327
macro_precision_class0 0.788962472406181
macro_precision_class1 0.80065533795626
macro_precision_class2 0.5701519213583557
macro_precision_class3 0.22756410256410256
macro_precision_class4 0.5918367346938775
macro_precision_class5 0.3684210526315789
macro_precision_class6 0.3864628820960699
macro_precision_class7 0.125
macro_precision_class8 0.3561643835616438
macro_precision_class9 0.32919254658385094
macro_precision_class10 0.4188034188034188
macro_precision_class11 0.8336896490290397
macro_precision_class12 0.6409219571370804
macro_precision_class13 0.5624142661179699
macro_precision_class14 0.6802395209580838
macro_precision_class15 0.3490136570561457
macro_precision_class16 0.5500967117988395
macro_precision_class17 0.7747409872954855
macro_precision_class18 0.45819112627986347
macro_precision_class19 0.701734957548911
macro_precision_class20 0.6671194680775006
macro_precision_class21 0.7157260543464896
macro_precision_class22 0.7975594177564124
macro_precision_class23 0.8219280953721956
macro_precision_class24 0.8061016627978588
macro_precision_class25 0.5932656333511491
macro_precision_class26 0.6185737976782753
macro_precision_class27 0.7150537634408602
macro_precision_class28 0.6351457619502331
macro_precision_class29 0.6567164179104478
macro_precision_class30 0.5172955974842768
macro_precision_class31 0.24
macro_precision_class32 0.1111111111111111
macro_precision_class33 0.6155606407322655
macro_precision_class34 0.7674929400690305
macro_precision_class35 0.6551724137931034
macro_precision_class36 0.9166666666666666
macro_precision_class37 0.5
macro_precision_class38 0.7688083470620538
macro_precision_class39 0.679570895522388
macro_precision_class40 0.9656488549618321
macro_precision_class41 0.7976190476190477
macro_precision_class42 0.9783691662296802
macro_recall_class0 0.6766376372586141
macro_recall_class1 0.6677046263345195
macro_recall_class2 0.4544159544159544
macro_recall_class3 0.1392156862745098
macro_recall_class4 0.3625
macro_recall_class5 0.051094890510948905
macro_recall_class6 0.3702928870292887
macro_recall_class7 0.0963855421686747
macro_recall_class8 0.16352201257861634
macro_recall_class9 0.36551724137931035
macro_recall_class10 0.2433774834437086
macro_recall_class11 0.8358302261716046
macro_recall_class12 0.48001211387038156
macro_recall_class13 0.6298003072196621
macro_recall_class14 0.25244444444444447
macro_recall_class15 0.32763532763532766
macro_recall_class16 0.4650098103335513
macro_recall_class17 0.6837401737691353
macro_recall_class18 0.30773638968481376
macro_recall_class19 0.5931819954754661
macro_recall_class20 0.5822413577935854
macro_recall_class21 0.8016802093375568
macro_recall_class22 0.695136243773806
macro_recall_class23 0.8859657998583426
macro_recall_class24 0.7981051101052977
macro_recall_class25 0.41495327102803736
macro_recall_class26 0.3166383701188455
macro_recall_class27 0.5432603044931303
macro_recall_class28 0.6245339813868714
macro_recall_class29 0.5972850678733032
macro_recall_class30 0.740990990990991
macro_recall_class31 0.2909090909090909
macro_recall_class32 0.05555555555555555
macro_recall_class33 0.1989644970414201
macro_recall_class34 0.6083063914449142
macro_recall_class35 0.29343629343629346
macro_recall_class36 0.41904761904761906
macro_recall_class37 0.5568862275449101
macro_recall_class38 0.602928509905254
macro_recall_class39 0.8349570200573065
macro_recall_class40 0.729106628242075
macro_recall_class41 0.5338645418326693
macro_recall_class42 0.8554071866582612
macro_f1_score_class0 0.7284957195271097
macro_f1_score_class1 0.7281610589417513
macro_f1_score_class2 0.5057471264367817
macro_f1_score_class3 0.17274939172749393
macro_f1_score_class4 0.4496124031007752
macro_f1_score_class5 0.08974358974358974
macro_f1_score_class6 0.3782051282051282
macro_f1_score_class7 0.10884353741496598
macro_f1_score_class8 0.22413793103448276
macro_f1_score_class9 0.3464052287581699
macro_f1_score_class10 0.3078534031413613
macro_f1_score_class11 0.834758565328398
macro_f1_score_class12 0.5489177489177489
macro_f1_score_class13 0.5942028985507246
macro_f1_score_class14 0.3682333873581848
macro_f1_score_class15 0.33798677443056574
macro_f1_score_class16 0.5039872408293461
macro_f1_score_class17 0.726401617546867
macro_f1_score_class18 0.36818649297223177
macro_f1_score_class19 0.6429084760093003
macro_f1_score_class20 0.6217972212041448
macro_f1_score_class21 0.7562686761075744
macro_f1_score_class22 0.7428338838705637
macro_f1_score_class23 0.8527463965718738
macro_f1_score_class24 0.8020834560859779
macro_f1_score_class25 0.48834139903211615
macro_f1_score_class26 0.4188658057271196
macro_f1_score_class27 0.6174298375184638
macro_f1_score_class28 0.6297951738446329
macro_f1_score_class29 0.6255924170616114
macro_f1_score_class30 0.6092592592592593
macro_f1_score_class31 0.26301369863013696
macro_f1_score_class32 0.07407407407407407
macro_f1_score_class33 0.30072666294019007
macro_f1_score_class34 0.6786903440621531
macro_f1_score_class35 0.4053333333333333
macro_f1_score_class36 0.5751633986928104
macro_f1_score_class37 0.5269121813031161
macro_f1_score_class38 0.6758387641805454
macro_f1_score_class39 0.7492928773463615
macro_f1_score_class40 0.8308702791461412
macro_f1_score_class41 0.639618138424821
macro_f1_score_class42 0.9127656321663354
macro_f1_score_class_avg 0.5293686193153101
macro_f2_score_class0 0.6964689375633331
macro_f2_score_class1 0.6906411453061118
macro_f2_score_class2 0.473645137342242
macro_f2_score_class3 0.15093537414965985
macro_f2_score_class4 0.39295392953929537
macro_f2_score_class5 0.06172839506172839
macro_f2_score_class6 0.37341772151898733
macro_f2_score_class7 0.10101010101010101
macro_f2_score_class8 0.18335684062059238
macro_f2_score_class9 0.3576248313090418
macro_f2_score_class10 0.2656306469100108
macro_f2_score_class11 0.8354012318129073
macro_f2_score_class12 0.5053886869459856
macro_f2_score_class13 0.6150615061506151
macro_f2_score_class14 0.2887646161667514
macro_f2_score_class15 0.33169887510816265
macro_f2_score_class16 0.4798542215023284
macro_f2_score_class17 0.7001889654354255
macro_f2_score_class18 0.32936702649656524
macro_f2_score_class19 0.6121200412158682
macro_f2_score_class20 0.5974440076321215
macro_f2_score_class21 0.7828765096699573
macro_f2_score_class22 0.7134608791882935
macro_f2_score_class23 0.8723722227757298
macro_f2_score_class24 0.7996917032455753
macro_f2_score_class25 0.44149232360194096
macro_f2_score_class26 0.3508936970837253
macro_f2_score_class27 0.5706818536433141
macro_f2_score_class28 0.6266278747575506
macro_f2_score_class29 0.6082949308755761
macro_f2_score_class30 0.6820066334991708
macro_f2_score_class31 0.27906976744186046
macro_f2_score_class32 0.06172839506172839
macro_f2_score_class33 0.23011120615911035
macro_f2_score_class34 0.6346323491256292
macro_f2_score_class35 0.3298611111111111
macro_f2_score_class36 0.4700854700854701
macro_f2_score_class37 0.544496487119438
macro_f2_score_class38 0.630119722747322
macro_f2_score_class39 0.7984436650591845
macro_f2_score_class40 0.7666666666666667
macro_f2_score_class41 0.5716723549488054
macro_f2_score_class42 0.8774631989841509
macro_f2_score_class_avg 0.5043128200383522
macro_f0_5_score_class0 0.7636099478677036
macro_f0_5_score_class1 0.7699917922260655
macro_f0_5_score_class2 0.5425170068027211
macro_f0_5_score_class3 0.20193401592718999
macro_f0_5_score_class4 0.5253623188405797
macro_f0_5_score_class5 0.1643192488262911
macro_f0_5_score_class6 0.38311688311688313
macro_f0_5_score_class7 0.11799410029498525
macro_f0_5_score_class8 0.28824833702882485
macro_f0_5_score_class9 0.3358681875792142
macro_f0_5_score_class10 0.3660358565737052
macro_f0_5_score_class11 0.8341168868786346
macro_f0_5_score_class12 0.6006518114294376
macro_f0_5_score_class13 0.5747126436781609
macro_f0_5_score_class14 0.5080500894454383
macro_f0_5_score_class15 0.3445176752546435
macro_f0_5_score_class16 0.5306762203313927
macro_f0_5_score_class17 0.7546532230076534
macro_f0_5_score_class18 0.41737913881548266
macro_f0_5_score_class19 0.6769581397005145
macro_f0_5_score_class20 0.6482201906243922
macro_f0_5_score_class21 0.7314100469931898
macro_f0_5_score_class22 0.774729293747306
macro_f0_5_score_class23 0.8339841889703782
macro_f0_5_score_class24 0.8044895585614124
macro_f0_5_score_class25 0.5463136135446403
macro_f0_5_score_class26 0.5194986072423399
macro_f0_5_score_class27 0.6725199963225154
macro_f0_5_score_class28 0.6329946539032104
macro_f0_5_score_class29 0.6439024390243903
macro_f0_5_score_class30 0.5505354752342704
macro_f0_5_score_class31 0.24870466321243523
macro_f0_5_score_class32 0.09259259259259259
macro_f0_5_score_class33 0.4338709677419355
macro_f0_5_score_class34 0.7293219631462818
macro_f0_5_score_class35 0.5255878284923928
macro_f0_5_score_class36 0.7407407407407407
macro_f0_5_score_class37 0.5104281009879253
macro_f0_5_score_class38 0.7287112221528211
macro_f0_5_score_class39 0.7058424571262475
macro_f0_5_score_class40 0.9068100358422939
macro_f0_5_score_class41 0.7258938244853738
macro_f0_5_score_class42 0.9510277420259198
macro_f0_5_score_class_avg 0.5664847378218727
macro_accuracy_class0 0.5729400448861814
macro_accuracy_class1 0.5725261551874455
macro_accuracy_class2 0.3384615384615385
macro_accuracy_class3 0.09454061251664447
macro_accuracy_class4 0.29
macro_accuracy_class5 0.04697986577181208
macro_accuracy_class6 0.233201581027668
macro_accuracy_class7 0.05755395683453238
macro_accuracy_class8 0.1262135922330097
macro_accuracy_class9 0.20948616600790515
macro_accuracy_class10 0.18193069306930693
macro_accuracy_class11 0.7163824942113018
macro_accuracy_class12 0.37828162291169454
macro_accuracy_class13 0.422680412371134
macro_accuracy_class14 0.22566547477155344
macro_accuracy_class15 0.20335985853227231
macro_accuracy_class16 0.3368869936034115
macro_accuracy_class17 0.5703537532355479
macro_accuracy_class18 0.22563025210084034
macro_accuracy_class19 0.47373995389695345
macro_accuracy_class20 0.4511652644811914
macro_accuracy_class21 0.6080643476444166
macro_accuracy_class22 0.5908796573022514
macro_accuracy_class23 0.7432937181663837
macro_accuracy_class24 0.6695653884746297
macro_accuracy_class25 0.3230500582072177
macro_accuracy_class26 0.2649147727272727
macro_accuracy_class27 0.4465811965811966
macro_accuracy_class28 0.4596357871631235
macro_accuracy_class29 0.45517241379310347
macro_accuracy_class30 0.4380825565912117
macro_accuracy_class31 0.15141955835962145
macro_accuracy_class32 0.038461538461538464
macro_accuracy_class33 0.1769736842105263
macro_accuracy_class34 0.5136497270054599
macro_accuracy_class35 0.25418060200668896
macro_accuracy_class36 0.4036697247706422
macro_accuracy_class37 0.3576923076923077
macro_accuracy_class38 0.5103900838497994
macro_accuracy_class39 0.5990953947368421
macro_accuracy_class40 0.7106741573033708
macro_accuracy_class41 0.47017543859649125
macro_accuracy_class42 0.839529782327465

InceptionV2 (5)

sample_precision 0.061403465
sample_recall 0.37187907
sample_f1_score 0.10120078
sample_f2_score 0.17065492
sample_f0_5_score 0.07276111
hamming_loss 0.3914423
subset_accuracy 0.0
sample_accuracy 0.055386625
one_error 0.9767292
coverage 30.515245
label_cardinality 2.9340966
ranking_loss 0.49969387
label_ranking_avg_precision 0.13373771
micro_precision 0.06135497609272832
macro_precision_class_avg 0.07678725496275689
micro_recall 0.3312699869754646
macro_recall_class_avg 0.39652233590200414
micro_f1_score 0.10353423262895232
micro_f2_score 0.17622176525121533
micro_f0_5_score 0.07329973365268969
micro_accuracy 0.054593251515673485
macro_accuracy_class_avg 0.045187435489439036
macro_precision_class0 0.02554870871702555
macro_precision_class1 0.13324273777969134
macro_precision_class2 0.017809878426270694
macro_precision_class3 0.001883408071748879
macro_precision_class4 0.0002717391304347826
macro_precision_class5 0.0010086576447843995
macro_precision_class6 0.005212659315480452
macro_precision_class7 0.0007260431684523871
macro_precision_class8 0.000853450377956596
macro_precision_class9 0.0008940719144800778
macro_precision_class10 0.004810442770234882
macro_precision_class11 0.48436327245434074
macro_precision_class12 0.025729412346819285
macro_precision_class13 0.007325141776937618
macro_precision_class14 0.018982297994643675
macro_precision_class15 0.005079873462583516
macro_precision_class16 0.036483378632657326
macro_precision_class17 0.20423728813559322
macro_precision_class18 0.012848908912436133
macro_precision_class19 0.21218243945921855
macro_precision_class20 0.25270195305775006
macro_precision_class21 0.03158011049723757
macro_precision_class22 0.23529411764705882
macro_precision_class23 0.4365930921724913
macro_precision_class24 0.3123297962249977
macro_precision_class25 0.02187849801567111
macro_precision_class26 0.007501487363873871
macro_precision_class27 0.03525524156791249
macro_precision_class28 0.18981641842743333
macro_precision_class29 0.002555818102907846
macro_precision_class30 0.003304154465306378
macro_precision_class31 0.00151846812202962
macro_precision_class32 0.0
macro_precision_class33 0.009957369062119366
macro_precision_class34 0.03360344275053793
macro_precision_class35 0.003576218149307108
macro_precision_class36 0.0008158166452089892
macro_precision_class37 0.0014140445362548723
macro_precision_class38 0.0230380827604481
macro_precision_class39 0.11937038938035285
macro_precision_class40 0.0036368722898307455
macro_precision_class41 0.0012401178111920632
macro_precision_class42 0.3753766438568331
macro_recall_class0 0.5588792124195381
macro_recall_class1 0.6293213014743264
macro_recall_class2 0.1413817663817664
macro_recall_class3 0.041176470588235294
macro_recall_class4 0.0125
macro_recall_class5 0.26277372262773724
macro_recall_class6 0.3807531380753138
macro_recall_class7 0.8433734939759037
macro_recall_class8 0.0440251572327044
macro_recall_class9 0.07931034482758621
macro_recall_class10 0.9884105960264901
macro_recall_class11 0.2161244948536471
macro_recall_class12 0.9448818897637795
macro_recall_class13 0.8095238095238095
macro_recall_class14 0.6457777777777778
macro_recall_class15 0.5512820512820513
macro_recall_class16 0.11412688031393067
macro_recall_class17 0.25924700041373605
macro_recall_class18 0.5851002865329513
macro_recall_class19 0.2785318667602777
macro_recall_class20 0.8243167353051292
macro_recall_class21 0.19680484781710508
macro_recall_class22 0.025080574274831527
macro_recall_class23 0.2161540018213093
macro_recall_class24 0.23400014070964564
macro_recall_class25 0.08037383177570094
macro_recall_class26 0.2461799660441426
macro_recall_class27 0.5744522836984776
macro_recall_class28 0.015133522962635664
macro_recall_class29 0.2398190045248869
macro_recall_class30 0.4752252252252252
macro_recall_class31 0.4666666666666667
macro_recall_class32 0.0
macro_recall_class33 0.7255917159763313
macro_recall_class34 0.3650833126088038
macro_recall_class35 0.06177606177606178
macro_recall_class36 0.6095238095238096
macro_recall_class37 0.688622754491018
macro_recall_class38 0.6739879414298019
macro_recall_class39 0.8496418338108882
macro_recall_class40 0.29971181556195964
macro_recall_class41 0.03187250996015936
macro_recall_class42 0.763940626970027
macro_f1_score_class0 0.04886365517355536
macro_f1_score_class1 0.21992249525311186
macro_f1_score_class2 0.03163472648312682
macro_f1_score_class3 0.003602058319039451
macro_f1_score_class4 0.0005319148936170213
macro_f1_score_class5 0.002009601429049905
macro_f1_score_class6 0.010284519537761704
macro_f1_score_class7 0.0014508373404078926
macro_f1_score_class8 0.001674440856356895
macro_f1_score_class9 0.0017682106477032481
macro_f1_score_class10 0.009574288944663176
macro_f1_score_class11 0.2988853552351252
macro_f1_score_class12 0.05009473041970393
macro_f1_score_class13 0.014518906260761761
macro_f1_score_class14 0.03688051272288851
macro_f1_score_class15 0.010066983156662547
macro_f1_score_class16 0.0552915082382763
macro_f1_score_class17 0.22847766636280767
macro_f1_score_class18 0.025145615525755168
macro_f1_score_class19 0.24087160373062588
macro_f1_score_class20 0.38682049104724536
macro_f1_score_class21 0.054426691550341834
macro_f1_score_class22 0.0453293793687778
macro_f1_score_class23 0.28915132647536546
macro_f1_score_class24 0.2675497399045423
macro_f1_score_class25 0.03439449688049912
macro_f1_score_class26 0.014559329266762056
macro_f1_score_class27 0.06643334120627832
macro_f1_score_class28 0.028032124405340426
macro_f1_score_class29 0.005057734516652352
macro_f1_score_class30 0.006562679812761457
macro_f1_score_class31 0.003027086527499312
macro_f1_score_class32 0.0
macro_f1_score_class33 0.019645145786606855
macro_f1_score_class34 0.06154233131406293
macro_f1_score_class35 0.006761039509824636
macro_f1_score_class36 0.0016294523512488225
macro_f1_score_class37 0.0028222936657913466
macro_f1_score_class38 0.044553257512134715
macro_f1_score_class39 0.20933085075403934
macro_f1_score_class40 0.007186539059530802
macro_f1_score_class41 0.002387347060578932
macro_f1_score_class42 0.5033987915407855
macro_f1_score_class_avg 0.07795700232668999
macro_f2_score_class0 0.10799578553032077
macro_f2_score_class1 0.3607203479350463
macro_f2_score_class2 0.05921307758852131
macro_f2_score_class3 0.00796057619408643
macro_f2_score_class4 0.00125
macro_f2_score_class5 0.004967024476392836
macro_f2_score_class6 0.024710131153773046
macro_f2_score_class7 0.003617758023670474
macro_f2_score_class8 0.0039601719846119035
macro_f2_score_class9 0.004277478147665985
macro_f2_score_class10 0.02359292133321741
macro_f2_score_class11 0.24304387343640937
macro_f2_score_class12 0.11601100617238046
macro_f2_score_class13 0.035346354026935665
macro_f2_score_class14 0.08492606230638845
macro_f2_score_class15 0.024496461622210124
macro_f2_score_class16 0.08005321589136619
macro_f2_score_class17 0.24599560301507536
macro_f2_score_class18 0.05905693991346799
macro_f2_score_class19 0.2621377755915628
macro_f2_score_class20 0.5675539821792217
macro_f2_score_class21 0.0961719654346246
macro_f2_score_class22 0.03053696542473494
macro_f2_score_class23 0.24043331457512662
macro_f2_score_class24 0.24635698801546582
macro_f2_score_class25 0.05237004920348809
macro_f2_score_class26 0.03343247792303613
macro_f2_score_class27 0.14153187440532825
macro_f2_score_class28 0.018547224346954938
macro_f2_score_class29 0.012256602377318349
macro_f2_score_class30 0.016073741144206597
macro_f2_score_class31 0.007494792579181997
macro_f2_score_class32 0.0
macro_f2_score_class33 0.047196135786313605
macro_f2_score_class34 0.12280408231554292
macro_f2_score_class35 0.014519056261343012
macro_f2_score_class36 0.004057360940293398
macro_f2_score_class37 0.0070126227208976155
macro_f2_score_class38 0.10133516362553258
macro_f2_score_class39 0.3821124591663821
macro_f2_score_class40 0.017342582710779084
macro_f2_score_class41 0.0053655264922870555
macro_f2_score_class42 0.6329113924050633
macro_f2_score_class_avg 0.10583137033433083
macro_f0_5_score_class0 0.03157502920048432
macro_f0_5_score_class1 0.15818073782537026
macro_f0_5_score_class2 0.021582655590831994
macro_f0_5_score_class3 0.0023276435380181777
macro_f0_5_score_class4 0.00033783783783783786
macro_f0_5_score_class5 0.0012596132987172939
macro_f0_5_score_class6 0.006493599166535843
macro_f0_5_score_class7 0.0009073586788857635
macro_f0_5_score_class8 0.001061667728334395
macro_f0_5_score_class9 0.001114449074522725
macro_f0_5_score_class10 0.006005746201390677
macro_f0_5_score_class11 0.3880412734809066
macro_f0_5_score_class12 0.031944302242244295
macro_f0_5_score_class13 0.009135760521726467
macro_f0_5_score_class14 0.023554777421132832
macro_f0_5_score_class15 0.006335247549388739
macro_f0_5_score_class16 0.04222932094283917
macro_f0_5_score_class17 0.21328885560623595
macro_f0_5_score_class18 0.01597344122055239
macro_f0_5_score_class19 0.22279698985386065
macro_f0_5_score_class20 0.29339193546954256
macro_f0_5_score_class21 0.03795262959402106
macro_f0_5_score_class22 0.0879175054434904
macro_f0_5_score_class23 0.3626294347309455
macro_f0_5_score_class24 0.29273187074969637
macro_f0_5_score_class25 0.025605602267584498
macro_f0_5_score_class26 0.009305967371353023
macro_f0_5_score_class27 0.0434031187399348
macro_f0_5_score_class28 0.057371385498021316
macro_f0_5_score_class29 0.0031862833507677137
macro_f0_5_score_class30 0.004123026418633735
macro_f0_5_score_class31 0.0018965423815646228
macro_f0_5_score_class32 0.0
macro_f0_5_score_class33 0.01240415545529952
macro_f0_5_score_class34 0.041059491511201855
macro_f0_5_score_class35 0.004406499586890664
macro_f0_5_score_class36 0.0010194296928012336
macro_f0_5_score_class37 0.0017666487441431753
macro_f0_5_score_class38 0.028553600490428613
macro_f0_5_score_class39 0.1441499032599924
macro_f0_5_score_class40 0.004532340866897351
macro_f0_5_score_class41 0.001535213970447131
macro_f0_5_score_class42 0.4178866784122187
macro_f0_5_score_class_avg 0.07118547839501611
macro_accuracy_class0 0.025043690720599962
macro_accuracy_class1 0.12354658416088628
macro_accuracy_class2 0.0160715731519715
macro_accuracy_class3 0.0018042787181029299
macro_accuracy_class4 0.00026602819898909286
macro_accuracy_class5 0.001005811354492624
macro_accuracy_class6 0.005168839283178552
macro_accuracy_class7 0.000725945284466845
macro_accuracy_class8 0.0008379219535551831
macro_accuracy_class9 0.0008848876577408433
macro_accuracy_class10 0.004810171458037901
macro_accuracy_class11 0.17569971321741024
macro_accuracy_class12 0.025690853397450676
macro_accuracy_class13 0.00731253815840595
macro_accuracy_class14 0.018786687698792378
macro_accuracy_class15 0.005058955789693848
macro_accuracy_class16 0.028431771894093686
macro_accuracy_class17 0.12897250123497447
macro_accuracy_class18 0.01273289601676103
macro_accuracy_class19 0.13692667587053228
macro_accuracy_class20 0.2397876298951763
macro_accuracy_class21 0.027974629027837594
macro_accuracy_class22 0.02319029042045947
macro_accuracy_class23 0.1690104630233984
macro_accuracy_class24 0.15443429809626993
macro_accuracy_class25 0.017498168796288762
macro_accuracy_class26 0.007333046754494652
macro_accuracy_class27 0.03435792653133745
macro_accuracy_class28 0.014215304798962387
macro_accuracy_class29 0.0025352786414733317
macro_accuracy_class30 0.0032921425450914313
macro_accuracy_class31 0.0015158375494615825
macro_accuracy_class32 0.0
macro_accuracy_class33 0.009920012943543902
macro_accuracy_class34 0.03174809143796362
macro_accuracy_class35 0.0033919864320542717
macro_accuracy_class36 0.0008153904956045356
macro_accuracy_class37 0.0014131409823173054
macro_accuracy_class38 0.022784183554623805
macro_accuracy_class39 0.11690090871459266
macro_accuracy_class40 0.003606227677797427
macro_accuracy_class41 0.0011951000896325067
macro_accuracy_class42 0.3363613424173606

InceptionV2 (4)

sample_precision 0.26506168
sample_recall 0.21288945
sample_f1_score 0.16714056
sample_f2_score 0.1666056
sample_f0_5_score 0.20219772
hamming_loss 0.08222219
subset_accuracy 0.014761731
sample_accuracy 0.11552983
one_error 0.5190838
coverage 13.576216
label_cardinality 2.9340966
ranking_loss 0.1377718
label_ranking_avg_precision 0.5187483
micro_precision 0.28204301384850144
macro_precision_class_avg 0.10380039582376403
micro_recall 0.1326309290744998
macro_recall_class_avg 0.042582342165866136
micro_f1_score 0.18041947223409116
micro_f2_score 0.14834842329258932
micro_f0_5_score 0.23018187662540895
micro_accuracy 0.09915443118948475
macro_accuracy_class_avg 0.03245732202625878
macro_precision_class0 0.001669449081803005
macro_precision_class1 0.005415028558712262
macro_precision_class2 0.0
macro_precision_class3 0.0
macro_precision_class4 0.0
macro_precision_class5 0.0
macro_precision_class6 0.0
macro_precision_class7 0.0
macro_precision_class8 0.0
macro_precision_class9 0.0
macro_precision_class10 0.0006016847172081829
macro_precision_class11 0.8261823987228099
macro_precision_class12 0.0020062009848623016
macro_precision_class13 0.0
macro_precision_class14 0.0
macro_precision_class15 0.0
macro_precision_class16 0.0
macro_precision_class17 0.5
macro_precision_class18 0.0
macro_precision_class19 0.27202472952086554
macro_precision_class20 0.17050561797752808
macro_precision_class21 0.0
macro_precision_class22 0.10071942446043165
macro_precision_class23 0.4922703732938028
macro_precision_class24 0.7283737024221453
macro_precision_class25 0.0
macro_precision_class26 0.0
macro_precision_class27 0.00023223409196470042
macro_precision_class28 0.28874388254486133
macro_precision_class29 0.0
macro_precision_class30 0.0
macro_precision_class31 0.0
macro_precision_class32 0.0
macro_precision_class33 0.0
macro_precision_class34 0.0
macro_precision_class35 0.0
macro_precision_class36 0.0
macro_precision_class37 0.0
macro_precision_class38 0.0
macro_precision_class39 0.1774572817055965
macro_precision_class40 0.018867924528301886
macro_precision_class41 0.0
macro_precision_class42 0.87834708781096
macro_recall_class0 0.007951533510034078
macro_recall_class1 0.004639044229791561
macro_recall_class2 0.0
macro_recall_class3 0.0
macro_recall_class4 0.0
macro_recall_class5 0.0
macro_recall_class6 0.0
macro_recall_class7 0.0
macro_recall_class8 0.0
macro_recall_class9 0.0
macro_recall_class10 0.0016556291390728477
macro_recall_class11 0.09243340998905981
macro_recall_class12 0.003331314354936402
macro_recall_class13 0.0
macro_recall_class14 0.0
macro_recall_class15 0.0
macro_recall_class16 0.0
macro_recall_class17 4.137360364087712e-05
macro_recall_class18 0.0
macro_recall_class19 0.006864810047585615
macro_recall_class20 0.01893797578934232
macro_recall_class21 0.0
macro_recall_class22 0.0004101963082332259
macro_recall_class23 0.6468177678842457
macro_recall_class24 0.009873126802842335
macro_recall_class25 0.0
macro_recall_class26 0.0
macro_recall_class27 0.0007426661715558856
macro_recall_class28 0.024440087266300297
macro_recall_class29 0.0
macro_recall_class30 0.0
macro_recall_class31 0.0
macro_recall_class32 0.0
macro_recall_class33 0.0
macro_recall_class34 0.0
macro_recall_class35 0.0
macro_recall_class36 0.0
macro_recall_class37 0.0
macro_recall_class38 0.0
macro_recall_class39 0.16217765042979942
macro_recall_class40 0.002881844380403458
macro_recall_class41 0.0
macro_recall_class42 0.8478422832253998
macro_f1_score_class0 0.002759526938239159
macro_f1_score_class1 0.004997090734846151
macro_f1_score_class2 0.0
macro_f1_score_class3 0.0
macro_f1_score_class4 0.0
macro_f1_score_class5 0.0
macro_f1_score_class6 0.0
macro_f1_score_class7 0.0
macro_f1_score_class8 0.0
macro_f1_score_class9 0.0
macro_f1_score_class10 0.00088261253309797
macro_f1_score_class11 0.16626506024096385
macro_f1_score_class12 0.002504268639726807
macro_f1_score_class13 0.0
macro_f1_score_class14 0.0
macro_f1_score_class15 0.0
macro_f1_score_class16 0.0
macro_f1_score_class17 8.274036074797286e-05
macro_f1_score_class18 0.0
macro_f1_score_class19 0.01339166825185467
macro_f1_score_class20 0.03408963270807593
macro_f1_score_class21 0.0
macro_f1_score_class22 0.0008170649858472673
macro_f1_score_class23 0.5590598524186936
macro_f1_score_class24 0.01948217219278558
macro_f1_score_class25 0.0
macro_f1_score_class26 0.0
macro_f1_score_class27 0.00035382574082264487
macro_f1_score_class28 0.04506568897036358
macro_f1_score_class29 0.0
macro_f1_score_class30 0.0
macro_f1_score_class31 0.0
macro_f1_score_class32 0.0
macro_f1_score_class33 0.0
macro_f1_score_class34 0.0
macro_f1_score_class35 0.0
macro_f1_score_class36 0.0
macro_f1_score_class37 0.0
macro_f1_score_class38 0.0
macro_f1_score_class39 0.16947376300621303
macro_f1_score_class40 0.005
macro_f1_score_class41 0.0
macro_f1_score_class42 0.8628251487227342
macro_f1_score_class_avg 0.04388488642895378
macro_f2_score_class0 0.004537009030808452
macro_f2_score_class1 0.004775924108603205
macro_f2_score_class2 0.0
macro_f2_score_class3 0.0
macro_f2_score_class4 0.0
macro_f2_score_class5 0.0
macro_f2_score_class6 0.0
macro_f2_score_class7 0.0
macro_f2_score_class8 0.0
macro_f2_score_class9 0.0
macro_f2_score_class10 0.0012260912211868563
macro_f2_score_class11 0.11239798660997899
macro_f2_score_class12 0.002942592691669788
macro_f2_score_class13 0.0
macro_f2_score_class14 0.0
macro_f2_score_class15 0.0
macro_f2_score_class16 0.0
macro_f2_score_class17 5.171593471380402e-05
macro_f2_score_class18 0.0
macro_f2_score_class19 0.008527214410992355
macro_f2_score_class20 0.023032906320199137
macro_f2_score_class21 0.0
macro_f2_score_class22 0.0005122238564602404
macro_f2_score_class23 0.6086037577771325
macro_f2_score_class24 0.012299727711491043
macro_f2_score_class25 0.0
macro_f2_score_class26 0.0
macro_f2_score_class27 0.0005158893933140734
macro_f2_score_class28 0.0299170435876113
macro_f2_score_class29 0.0
macro_f2_score_class30 0.0
macro_f2_score_class31 0.0
macro_f2_score_class32 0.0
macro_f2_score_class33 0.0
macro_f2_score_class34 0.0
macro_f2_score_class35 0.0
macro_f2_score_class36 0.0
macro_f2_score_class37 0.0
macro_f2_score_class38 0.0
macro_f2_score_class39 0.1650193883203592
macro_f2_score_class40 0.0034698126301179735
macro_f2_score_class41 0.0
macro_f2_score_class42 0.853772550468034
macro_f2_score_class_avg 0.04259539149006216
macro_f0_5_score_class0 0.001982740714164322
macro_f0_5_score_class1 0.005239735859890899
macro_f0_5_score_class2 0.0
macro_f0_5_score_class3 0.0
macro_f0_5_score_class4 0.0
macro_f0_5_score_class5 0.0
macro_f0_5_score_class6 0.0
macro_f0_5_score_class7 0.0
macro_f0_5_score_class8 0.0
macro_f0_5_score_class9 0.0
macro_f0_5_score_class10 0.0006894649751792609
macro_f0_5_score_class11 0.3192818472074406
macro_f0_5_score_class12 0.002179598953792502
macro_f0_5_score_class13 0.0
macro_f0_5_score_class14 0.0
macro_f0_5_score_class15 0.0
macro_f0_5_score_class16 0.0
macro_f0_5_score_class17 0.0002067995698568947
macro_f0_5_score_class18 0.0
macro_f0_5_score_class19 0.03117692907248636
macro_f0_5_score_class20 0.06556208416141018
macro_f0_5_score_class21 0.0
macro_f0_5_score_class22 0.002018105287435853
macro_f0_5_score_class23 0.5169750590366512
macro_f0_5_score_class24 0.0468266856494561
macro_f0_5_score_class25 0.0
macro_f0_5_score_class26 0.0
macro_f0_5_score_class27 0.00026924423144234134
macro_f0_5_score_class28 0.0912917001918673
macro_f0_5_score_class29 0.0
macro_f0_5_score_class30 0.0
macro_f0_5_score_class31 0.0
macro_f0_5_score_class32 0.0
macro_f0_5_score_class33 0.0
macro_f0_5_score_class34 0.0
macro_f0_5_score_class35 0.0
macro_f0_5_score_class36 0.0
macro_f0_5_score_class37 0.0
macro_f0_5_score_class38 0.0
macro_f0_5_score_class39 0.1741752831117676
macro_f0_5_score_class40 0.008944543828264758
macro_f0_5_score_class41 0.0
macro_f0_5_score_class42 0.8720717746784404
macro_f0_5_score_class_avg 0.049741665035570856
macro_accuracy_class0 0.0013816698467004407
macro_accuracy_class1 0.002504803733186934
macro_accuracy_class2 0.0
macro_accuracy_class3 0.0
macro_accuracy_class4 0.0
macro_accuracy_class5 0.0
macro_accuracy_class6 0.0
macro_accuracy_class7 0.0
macro_accuracy_class8 0.0
macro_accuracy_class9 0.0
macro_accuracy_class10 0.0004415011037527594
macro_accuracy_class11 0.09067017082785808
macro_accuracy_class12 0.001253704125826305
macro_accuracy_class13 0.0
macro_accuracy_class14 0.0
macro_accuracy_class15 0.0
macro_accuracy_class16 0.0
macro_accuracy_class17 4.137189193661826e-05
macro_accuracy_class18 0.0
macro_accuracy_class19 0.006740970546554828
macro_accuracy_class20 0.01734037994572204
macro_accuracy_class21 0.0
macro_accuracy_class22 0.0004086994599328565
macro_accuracy_class23 0.3879827023746302
macro_accuracy_class24 0.009836908266741436
macro_accuracy_class25 0.0
macro_accuracy_class26 0.0
macro_accuracy_class27 0.00017694417411306734
macro_accuracy_class28 0.023052277877627567
macro_accuracy_class29 0.0
macro_accuracy_class30 0.0
macro_accuracy_class31 0.0
macro_accuracy_class32 0.0
macro_accuracy_class33 0.0
macro_accuracy_class34 0.0
macro_accuracy_class35 0.0
macro_accuracy_class36 0.0
macro_accuracy_class37 0.0
macro_accuracy_class38 0.0
macro_accuracy_class39 0.09258199067637196
macro_accuracy_class40 0.002506265664160401
macro_accuracy_class41 0.0
macro_accuracy_class42 0.7587444866140117

InceptionV2 (6)

sample_precision 0.7057715
sample_recall 0.5031526
sample_f1_score 0.5565762
sample_f2_score 0.5189293
sample_f0_5_score 0.62278146
hamming_loss 0.04663838
subset_accuracy 0.18978915
sample_accuracy 0.46152836
one_error 0.18108147
coverage 6.7898
label_cardinality 2.9340966
ranking_loss 0.0462609
label_ranking_avg_precision 0.7770205
micro_precision 0.7720875447523895
macro_precision_class_avg 0.2345627926401323
micro_recall 0.4490594444128534
macro_recall_class_avg 0.13500899172644826
micro_f1_score 0.567848435873555
micro_f2_score 0.49006651871596124
micro_f0_5_score 0.6749791408046562
micro_accuracy 0.3965002378920138
macro_accuracy_class_avg 0.11885304537969538
macro_precision_class0 0.0
macro_precision_class1 0.8608519900497512
macro_precision_class2 0.7272727272727273
macro_precision_class3 0.0
macro_precision_class4 0.0
macro_precision_class5 0.0
macro_precision_class6 0.0
macro_precision_class7 0.0
macro_precision_class8 0.0
macro_precision_class9 0.0
macro_precision_class10 0.0
macro_precision_class11 0.7783562148714641
macro_precision_class12 0.0
macro_precision_class13 0.0
macro_precision_class14 0.0
macro_precision_class15 0.0
macro_precision_class16 0.0
macro_precision_class17 0.7950136612021858
macro_precision_class18 0.0
macro_precision_class19 0.6234558487113009
macro_precision_class20 0.6329276958607213
macro_precision_class21 0.7624581005586593
macro_precision_class22 0.749025552187094
macro_precision_class23 0.8908869375236335
macro_precision_class24 0.7851295832947209
macro_precision_class25 0.0
macro_precision_class26 0.0
macro_precision_class27 0.0
macro_precision_class28 0.7193466270683072
macro_precision_class29 0.0
macro_precision_class30 0.0
macro_precision_class31 0.0
macro_precision_class32 0.0
macro_precision_class33 0.0
macro_precision_class34 0.0
macro_precision_class35 0.0
macro_precision_class36 0.0
macro_precision_class37 0.0
macro_precision_class38 0.0
macro_precision_class39 0.7850901146914254
macro_precision_class40 0.0
macro_precision_class41 0.0
macro_precision_class42 0.9763850302336983
macro_recall_class0 0.0
macro_recall_class1 0.35186832740213525
macro_recall_class2 0.002849002849002849
macro_recall_class3 0.0
macro_recall_class4 0.0
macro_recall_class5 0.0
macro_recall_class6 0.0
macro_recall_class7 0.0
macro_recall_class8 0.0
macro_recall_class9 0.0
macro_recall_class10 0.0
macro_recall_class11 0.754426310031481
macro_recall_class12 0.0
macro_recall_class13 0.0
macro_recall_class14 0.0
macro_recall_class15 0.0
macro_recall_class16 0.0
macro_recall_class17 0.4815473727761688
macro_recall_class18 0.0
macro_recall_class19 0.4783524455885795
macro_recall_class20 0.44462124048421314
macro_recall_class21 0.46990772620851123
macro_recall_class22 0.40539115147963667
macro_recall_class23 0.536375594455125
macro_recall_class24 0.5960695105649493
macro_recall_class25 0.0
macro_recall_class26 0.0
macro_recall_class27 0.0
macro_recall_class28 0.18729115462152385
macro_recall_class29 0.0
macro_recall_class30 0.0
macro_recall_class31 0.0
macro_recall_class32 0.0
macro_recall_class33 0.0
macro_recall_class34 0.0
macro_recall_class35 0.0
macro_recall_class36 0.0
macro_recall_class37 0.0
macro_recall_class38 0.0
macro_recall_class39 0.41189111747851004
macro_recall_class40 0.0
macro_recall_class41 0.0
macro_recall_class42 0.6847956902974383
macro_f1_score_class0 0.0
macro_f1_score_class1 0.49954889931432694
macro_f1_score_class2 0.005675771550195104
macro_f1_score_class3 0.0
macro_f1_score_class4 0.0
macro_f1_score_class5 0.0
macro_f1_score_class6 0.0
macro_f1_score_class7 0.0
macro_f1_score_class8 0.0
macro_f1_score_class9 0.0
macro_f1_score_class10 0.0
macro_f1_score_class11 0.7662044648019863
macro_f1_score_class12 0.0
macro_f1_score_class13 0.0
macro_f1_score_class14 0.0
macro_f1_score_class15 0.0
macro_f1_score_class16 0.0
macro_f1_score_class17 0.5997938675599073
macro_f1_score_class18 0.0
macro_f1_score_class19 0.5413494007813018
macro_f1_score_class20 0.522320774080047
macro_f1_score_class21 0.5814587593728698
macro_f1_score_class22 0.5260636477700468
macro_f1_score_class23 0.6696035242290749
macro_f1_score_class24 0.6776601701015811
macro_f1_score_class25 0.0
macro_f1_score_class26 0.0
macro_f1_score_class27 0.0
macro_f1_score_class28 0.29720195446876574
macro_f1_score_class29 0.0
macro_f1_score_class30 0.0
macro_f1_score_class31 0.0
macro_f1_score_class32 0.0
macro_f1_score_class33 0.0
macro_f1_score_class34 0.0
macro_f1_score_class35 0.0
macro_f1_score_class36 0.0
macro_f1_score_class37 0.0
macro_f1_score_class38 0.0
macro_f1_score_class39 0.5403119714339409
macro_f1_score_class40 0.0
macro_f1_score_class41 0.0
macro_f1_score_class42 0.8049988210327753
macro_f1_score_class_avg 0.1635393494534144
macro_f2_score_class0 0.0
macro_f2_score_class1 0.39905731088560886
macro_f2_score_class2 0.0035577692786622787
macro_f2_score_class3 0.0
macro_f2_score_class4 0.0
macro_f2_score_class5 0.0
macro_f2_score_class6 0.0
macro_f2_score_class7 0.0
macro_f2_score_class8 0.0
macro_f2_score_class9 0.0
macro_f2_score_class10 0.0
macro_f2_score_class11 0.7590938499694475
macro_f2_score_class12 0.0
macro_f2_score_class13 0.0
macro_f2_score_class14 0.0
macro_f2_score_class15 0.0
macro_f2_score_class16 0.0
macro_f2_score_class17 0.5227721882860222
macro_f2_score_class18 0.0
macro_f2_score_class19 0.5017058982351931
macro_f2_score_class20 0.472751519333351
macro_f2_score_class21 0.5089650645902324
macro_f2_score_class22 0.44634561783834004
macro_f2_score_class23 0.5827548109427304
macro_f2_score_class24 0.6262288296367838
macro_f2_score_class25 0.0
macro_f2_score_class26 0.0
macro_f2_score_class27 0.0
macro_f2_score_class28 0.21980657539929474
macro_f2_score_class29 0.0
macro_f2_score_class30 0.0
macro_f2_score_class31 0.0
macro_f2_score_class32 0.0
macro_f2_score_class33 0.0
macro_f2_score_class34 0.0
macro_f2_score_class35 0.0
macro_f2_score_class36 0.0
macro_f2_score_class37 0.0
macro_f2_score_class38 0.0
macro_f2_score_class39 0.45516433411436896
macro_f2_score_class40 0.0
macro_f2_score_class41 0.0
macro_f2_score_class42 0.7282955847575395
macro_f2_score_class_avg 0.1448023105411064
macro_f0_5_score_class0 0.0
macro_f0_5_score_class1 0.6676876326451862
macro_f0_5_score_class2 0.014025245441795231
macro_f0_5_score_class3 0.0
macro_f0_5_score_class4 0.0
macro_f0_5_score_class5 0.0
macro_f0_5_score_class6 0.0
macro_f0_5_score_class7 0.0
macro_f0_5_score_class8 0.0
macro_f0_5_score_class9 0.0
macro_f0_5_score_class10 0.0
macro_f0_5_score_class11 0.7734495529603501
macro_f0_5_score_class12 0.0
macro_f0_5_score_class13 0.0
macro_f0_5_score_class14 0.0
macro_f0_5_score_class15 0.0
macro_f0_5_score_class16 0.0
macro_f0_5_score_class17 0.7034328538619606
macro_f0_5_score_class18 0.0
macro_f0_5_score_class19 0.5877954793811468
macro_f0_5_score_class20 0.5835025713256248
macro_f0_5_score_class21 0.6780334644886928
macro_f0_5_score_class22 0.6404488140865411
macro_f0_5_score_class23 0.7868721054506591
macro_f0_5_score_class24 0.738295387868683
macro_f0_5_score_class25 0.0
macro_f0_5_score_class26 0.0
macro_f0_5_score_class27 0.0
macro_f0_5_score_class28 0.4587205605833097
macro_f0_5_score_class29 0.0
macro_f0_5_score_class30 0.0
macro_f0_5_score_class31 0.0
macro_f0_5_score_class32 0.0
macro_f0_5_score_class33 0.0
macro_f0_5_score_class34 0.0
macro_f0_5_score_class35 0.0
macro_f0_5_score_class36 0.0
macro_f0_5_score_class37 0.0
macro_f0_5_score_class38 0.0
macro_f0_5_score_class39 0.6646476789347142
macro_f0_5_score_class40 0.0
macro_f0_5_score_class41 0.0
macro_f0_5_score_class42 0.899760545774913
macro_f0_5_score_class_avg 0.19062027657682737
macro_accuracy_class0 0.0
macro_accuracy_class1 0.3329324754975648
macro_accuracy_class2 0.002845962290999644
macro_accuracy_class3 0.0
macro_accuracy_class4 0.0
macro_accuracy_class5 0.0
macro_accuracy_class6 0.0
macro_accuracy_class7 0.0
macro_accuracy_class8 0.0
macro_accuracy_class9 0.0
macro_accuracy_class10 0.0
macro_accuracy_class11 0.6210141331716014
macro_accuracy_class12 0.0
macro_accuracy_class13 0.0
macro_accuracy_class14 0.0
macro_accuracy_class15 0.0
macro_accuracy_class16 0.0
macro_accuracy_class17 0.42836112031209744
macro_accuracy_class18 0.0
macro_accuracy_class19 0.371130276895143
macro_accuracy_class20 0.3534737207629536
macro_accuracy_class21 0.40989908697741473
macro_accuracy_class22 0.35691069493886396
macro_accuracy_class23 0.5033112582781457
macro_accuracy_class24 0.5124705123293748
macro_accuracy_class25 0.0
macro_accuracy_class26 0.0
macro_accuracy_class27 0.0
macro_accuracy_class28 0.1745374063875235
macro_accuracy_class29 0.0
macro_accuracy_class30 0.0
macro_accuracy_class31 0.0
macro_accuracy_class32 0.0
macro_accuracy_class33 0.0
macro_accuracy_class34 0.0
macro_accuracy_class35 0.0
macro_accuracy_class36 0.0
macro_accuracy_class37 0.0
macro_accuracy_class38 0.0
macro_accuracy_class39 0.3701557873052659
macro_accuracy_class40 0.0
macro_accuracy_class41 0.0
macro_accuracy_class42 0.6736385161799526

-->

## Application de notre méthode de classification

### Méthode appliquée


### Préparation des données


### Résultats



## Performances