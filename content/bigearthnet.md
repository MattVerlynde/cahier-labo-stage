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

<iframe src="../bigearthnet/subsambles_content_interact.html"
width="1200" height="900" style="border: none;"></iframe>

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

| Simulation | Modèle | Optimiseur | Fonction de coût | Taux d'apprentissage | Taille de batch | Epoques | Echantillon d'apprentissage | Echantillon de validation | Echantillon de test | Commentaire |
|:-----:|------------------|------|------------------------|:------:|:--:|:---:|:--------------:|:------------:|:------------:|-------------|
| **A** | **S-CNN-All**    | Adam | Cross-entropy sigmoïde | 0.0001 | 64 | 100 | **5%** (26969) | 25% (123723) | 25% (125866) |             |
| **B** | **S-CNN-All**    | Adam | Cross-entropy sigmoïde | 0.001  | 64 | 100 | **5%** (26969) | 25% (123723) | 25% (125866) |             |
| **C** | **S-CNN-All**    | Adam | Cross-entropy sigmoïde | 0.01   | 64 | 100 | **5%** (26969) | 25% (123723) | 25% (125866) |             |
| **D** | **S-CNN-RGB**    | Adam | Cross-entropy sigmoïde | 0.0001 | 64 | 100 | **5%** (26969) | 25% (123723) | 25% (125866) |             |
| **E** | **S-CNN-RGB**    | Adam | Cross-entropy sigmoïde | 0.001  | 64 | 100 | **5%** (26969) | 25% (123723) | 25% (125866) |             |
| **F** | **S-CNN-RGB**    | Adam | Cross-entropy sigmoïde | 0.01   | 64 | 100 | **5%** (26969) | 25% (123723) | 25% (125866) |             |
| **G** | **InceptionV2**  | Adam | Cross-entropy sigmoïde | 0.0001 | 64 | 100 | **5%** (26969) | 25% (123723) | 25% (125866) |             |
| **H** | **InceptionV2**  | Adam | Cross-entropy sigmoïde | 0.001  | 64 | 100 | **5%** (26969) | 25% (123723) | 25% (125866) |             |
| **I** | **InceptionV2**  | Adam | Cross-entropy sigmoïde | 0.01   | 64 | 100 | **5%** (26969) | 25% (123723) | 25% (125866) |             |
| **J** | **InceptionV2**  | Adam | Cross-entropy sigmoïde | 0.0001 | 64 | 100 | **5%** (26969) | 25% (123723) | 25% (125866) | Fine-tuned  |
| **K** | **InceptionV2**  | Adam | Cross-entropy sigmoïde | 0.001  | 64 | 100 | **5%** (26969) | 25% (123723) | 25% (125866) | Fine-tuned  |
| **L** | **InceptionV2**  | Adam | Cross-entropy sigmoïde | 0.01   | 64 | 100 | **5%** (26969) | 25% (123723) | 25% (125866) | Fine-tuned  |

#### Caractéristiques computationnelles

CPU : Intel i5-12600 3.30GHz
GPU : NVIDIA T400 4GB
RAM : 2 32 GB

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

| Simulation         | Précision (%) | Rappel (%) | F1     | F2     | F0.5   | Durée d'exécution (s) | Commentaire |
|--------------------|:-------------:|:----------:|:------:|:------:|:------:|:------:|-----------------------|
| **S-CNN-All (A0)** | 66.60         | 61.01      | 0.6074 | 0.6025 | 0.6315 | 27245.5799 | Réalisé sur CPU |
| **S-CNN-All (A0)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-All (A1)** | 66.74         | 60.53      | 0.6054 | 0.5989 | 0.6312 | 13666.1457 | Réalisé sur GPU |
| **S-CNN-All (A2)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-All (B0)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-All (B1)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-All (B2)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-All (C0)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-All (C1)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-All (C2)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-RGB (D0)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-RGB (D1)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-RGB (D2)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-RGB (E0)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-RGB (E1)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-RGB (E2)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-RGB (F0)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-RGB (F1)** |          |       |  |  |  |  | Réalisé sur GPU |
| **S-CNN-RGB (F2)** |          |       |  |  |  |  | Réalisé sur GPU |


Résultats de la littérature {{<cite "sumbul2019">}}, réalisé sur **l'ensemble de la base de données BigEarthNet** :

| Méthode              | Précision (%) | Rappel (%) | F1     | F2     |
|----------------------|:-------------:|:----------:|:------:|:------:|
| **Inception-v2**     | 48.23         | 56.79      | 0.4988 | 0.5301 |
| **S-CNN-RGB**        | 65.06         | 75.57      | 0.6759 | 0.7139 |
| **S-CNN-All**        | 69.93         | 77.10      | 0.7098 | 0.7384 |


<!--
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
<iframe src="../bigearthnet/training_losses_S-CNN-All_epochs_val_compar.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle S-CNN-RGB (F) :
<iframe src="../bigearthnet/training_losses_S-CNN-RGB_epochs_val_lr2_compar.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle S-CNN-RGB (D) :
<iframe src="../bigearthnet/training_losses_S-CNN-RGB_epochs_val_lr4_compar.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle S-CNN-RGB (D1-E1-F1) :
<iframe src="../bigearthnet/training_losses_S-CNN-RGB_epochs_val_s1_compar.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle InceptionV2 (I) :
<iframe src="../bigearthnet/training_losses_InceptionV2_epochs_val_lr2.html"
width="1000" height="500" style="border: none;"></iframe>
-->

Evolution de la fonction de coût au cours de l'entraînement pour le modèle InceptionV2 (H) :
<iframe src="../bigearthnet/training_losses_InceptionV2_epochs_val_lr3_compar_stat.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle InceptionV2 (K) :
<iframe src="../bigearthnet/training_losses_InceptionV2_epochs_val_lr3_compar_ft_stat.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle InceptionV2 (H-K) :
<iframe src="../bigearthnet/training_losses_InceptionV2_epochs_val_lr3_compar_stat_ft_nft.html"
width="1100" height="600" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle S-CNN-All (B) :
<iframe src="../bigearthnet/training_losses_S-CNN-All_epochs_val_lr3_compar_stat.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle S-CNN-All (A) :
<iframe src="../bigearthnet/training_losses_S-CNN-All_epochs_val_lr4_compar_stat.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle S-CNN-RGB (E) :
<iframe src="../bigearthnet/training_losses_S-CNN-RGB_epochs_val_lr3_compar_stat.html"
width="1000" height="500" style="border: none;"></iframe>

Evolution de la fonction de coût au cours de l'entraînement pour le modèle S-CNN-RGB (F) :
<iframe src="../bigearthnet/training_losses_S-CNN-RGB_epochs_val_lr2_compar_stat.html"
width="1000" height="500" style="border: none;"></iframe>



## Application de notre méthode de classification

### Méthode appliquée


### Préparation des données


### Résultats



## Performances