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

Hard filter pruning : on coupe des filtres du réseau de manière récursive à mesure qu'on fine-tune le modèle 
<!--
[Li et al., 2017] Hao Li, Asim Kadav, Igor Durdanovic, Hanan Samet, and Hans Peter Graf. Pruning filters for efficient ConvNets. In ICLR, 2017. 
[Liu et al., 2017] Zhuang Liu, Jianguo Li, Zhiqiang Shen, Gao Huang, Shoumeng Yan, and Changshui Zhang. Learning efficient convolutional networks through network slimming. In ICCV, 2017.
[Han et al., 2015a] Song Han, Huizi Mao, and William J Dally. Deep compression: Compressing deep neural networks with pruning, trained quantization and huffman coding. In ICLR, 2015. 
[Han et al., 2015b] Song Han, Jeff Pool, John Tran, and William Dally. Learning both weights and connections for efficient neural network. In NIPS, 2015. 
[He et al., 2016a] Kaiming He, Xiangyu Zhang, Shaoqing Ren, and Jian Sun. Deep residual learning for image recognition. In CVPR, 2016. 
[He et al., 2016b] Kaiming He, Xiangyu Zhang, Shaoqing Ren, and Jian Sun. Identity mappings in deep residual networks. In ECCV, 2016
-->

Ici soft filter pruning {{<cite "he2018">}} : on continue de mettre à jour les _pruned filters_
Chaque interval (nombre d'époques), on calcule l'importance ($\mathcal{l}_2$-norm) de chaque filtre pour chaque couche $i$, et on met à zéro une part $P_i$ prédéfinie de filtres, puis on réapprend à l'époque suivante.   

[Jaderberg et al., 2014; Zhang et al., 2016; Tai et al., 2016]


## matrix decomposition

## low-precision weights 

## pruning