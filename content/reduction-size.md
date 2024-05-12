---
layout: page
title: VII. Bibliographie - Réduction de dimension ou de taille d'architecture
menu:
  main:
    weight: 6
bibFile: content/bibliography.json
toc: True
---

Notes de lectures de bibliographie sur les méthodes de réduction de dimension ou de taille d'architecture autres que par l'utilisation de _covariance pooling_.

<!--more-->

## Bibliographie

### _Image classifcation using regularized convolutional neural network design with dimensionality reduction modules: RCNN–DRM_, Tulasi Krishna Sajja, Hemantha Kumar Kalluri

Ici {{<cite "sajja2021">}} réduction de dimension au sein du réseau de convolution par l'utilisation de 9 modules de réduction de dimenstion (_DR-module_) successifs. Il s'agit de plusieurs kernels convolutions en parallèle au sein d'un bloc, de différente taille (1x1, 3x3, 5x5), et dont les résultats sont concaténés ensuite en fin de bloc. C'est ce type d'architecture qu'on retrouve dans les réseaux Inception.

### _Soft Filter Pruning for Accelerating Deep Convolutional Neural Networks_, Yang He, Guoliang Kang, Xuanyi Dong, Yanwei Fu, Yi Yang

Pruning : on élimine des poids, connections, voire neurones, redondants ou "inutiles" dans notre modèle afin de diminuer sa taille et le nombre de calculs effectués (utilisé plutôt en IA embarquée)

Ici soft-pruning {{<cite "he2018">}}