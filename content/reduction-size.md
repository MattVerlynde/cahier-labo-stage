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

L'utilisation croissante de l'apprentissage profond est observée avec une croissance de la complexité des algorithmes utilisés et du nombre de paramètres liés à ces types de modèle. Ceci entraîne une augmentation de la capacité de calcul et de la consommation énergétique de ces algorithmes, que cela soit au niveau de l'apprentissage {{<cite "gholami2024">}}{{<cite "thompson2022">}}{{<cite "lohn2022">}} ou de l'inférence {{<cite "desislavov2023">}}, et donc également du coût écologique de ces algorithmes {{<cite "thompson2022">}}.
Les récents progrès en termes de baisse de consommation énergétique et d'augmentation de performances de processeurs permettent de diminuer les coûts computationnels et écologiques des approches d'apprentissage profond {{<cite "haut2019">}} mais la vitesse d'évolution de ce type de produit reste insuffisante en comparaison avec l'évolution des modèles [figure] {{<cite "gholami2024">}}. La complexité computationnelle ainsi consommation énergétique, donc l'impact écologique, des algorithmes d'apprentissage automatique, et en particulier ceux en apprentissage profond, s'impose donc comme un enjeu environnemental crucial au vu de l'utilisation croissante et accélérée au cours des dernières décennies. 

{{<cite "nalepa2020">}}

## low-precision weights
<!--
[Zhu et al., 2017] Chenzhuo Zhu, Song Han, Huizi Mao, and William J Dally. Trained ternary quantization. In ICLR, 2017.
[Zhou et al., 2017] Low-rank Decomposition approximates weight matrix in neural networks with low-rank matrix using techniques like Singular Value Decomposition (SVD)
[Courbariaux et al., 2016] M. Courbariaux and Y. Bengio. Binarynet: Training deep neural networks with weights and activations constrained to +1 or -1. arXiv preprint arXiv:1602.02830, 2016.
-->
Une première approche est celle de la réduction de la précision des poids au sein du réseau dans un objectof de réduction de la mémoire nécessaire aux calculs lors des phases de propagation et de rétropropagation du gradient. Une façon de diminuer la mémoire nécessaire est de convertir les poids au sein du réseau sous un format 8-bit{{<cite "vanhoucke2011">}} voire binaire \cite{{<cite "courbariaux2016">}}, mais la vitesse d'apprentissage  et la précision se retrouve diminuée.


[Vanhoucke et al., 2011] V. Vanhoucke, A. Senior, and M. Z. Mao. Improving the speed of neural networks on cpus. In Proc. Deep Learning and Unsupervised Feature Learning NIPS Workshop, 2011.
8-bit quantization of the layer weights can result in a speedup with minimal loss of accuracy


## matrix decomposition

{{<cite "jaderberg2014">}}
[Jaderberg et al., 2014] Max Jaderberg, Andrea Vedaldi, and Andrew Zisserman. Speeding up convolutional neural networks with low rank expansions. In BMVC, 2014
Décompostion de la matrice de poids afin de diminuer la complexité de l'algorithme de calcul (approximation par la somme des produits), des filtres de taille $k \times k$ sont approximés par l'application de deux filtres de tailles $k \times 1$ et $1 \times k$.

{{<cite "zhang2015">}}
[Zhang et al., 2016] Xiangyu Zhang, Jianhua Zou, Kaiming He, and Jian Sun. Accelerating very deep convolutional networks for classification and detection. IEEE T-PAMI, 2016.
Generalized Singular Value Decomposition (GSVD) 

{{<cite "tai2016">}}
[Tai et al., 2016] Cheng Tai, Tong Xiao, Yi Zhang, Xiaogang Wang, et al. Convolutional neural networks with low-rank regularization. In ICLR, 2016.
La décomposition de tenseurs permet une diminution de la redondance au sein du réseau
retour sur Jaderberg, décomposition en deux matrices horizontales et verticales
Problème d'augmentation de la profondeur du réseau, pouvant entraîner un phénomène de disparition du gradient
_Batch normalization_ : {{<cite "ioffe2015">}} Ioffe, Sergey and Szegedy, Christian. Batch normalization: Accelerating deep network training by reducing internal covariate shift. arXiv preprint arXiv:1502.03167, 2015.
On normalise l'input au niveau du sous-réseau, c''est à dire nous fixons la distribution en entrée d'une convolution, le but étant de réduire le _covariate shift_, mais aussi la disparition du gradient.

## low-rank decomposition

Low-rank Decomposition approximates weight matrix in neural networks with low-rank matrix using techniques like Singular Value Decomposition (SVD)


## dimension reduction

Ici {{<cite "sajja2021">}} réduction de dimension au sein du réseau de convolution par l'utilisation de 9 modules de réduction de dimenstion (_DR-module_) successifs. Il s'agit de plusieurs kernels convolutions en parallèle au sein d'un bloc, de différente taille (1x1, 3x3, 5x5), et dont les résultats sont concaténés ensuite en fin de bloc. C'est ce type d'architecture qu'on retrouve dans les réseaux Inception.

## pruning

Pruning : on élimine des poids, connections, voire neurones, redondants ou "inutiles" dans notre modèle afin de diminuer sa taille et le nombre de calculs effectués (utilisé plutôt en IA embarquée)

{{<cite "han2015">}}
[Han et al., 2015] S. Han, J. Pool, J. Tran, and W. Dally. Learning both weights and connections for efficient neural network. In NIPS, pages 1135–1143, 2015.

Weight pruning : on supprime les connections les moins utiles : entraîne le maximum de compression, mais introduit de la sparsité dans le réseau, nécessitant l'utilisation de packages et de matériel de support spécifiques 

Hard filter pruning : on coupe des filtres du réseau de manière récursive à mesure qu'on fine-tune le modèle 

{{<cite "li2017">}}
[Li et al., 2017] Hao Li, Asim Kadav, Igor Durdanovic, Hanan Samet, and Hans Peter Graf. Pruning filters for efficient ConvNets. In ICLR, 2017. 
{{<cite "liu2017">}}
[Liu et al., 2017] Zhuang Liu, Jianguo Li, Zhiqiang Shen, Gao Huang, Shoumeng Yan, and Changshui Zhang. Learning efficient convolutional networks through network slimming. In ICCV, 2017.

* Atouts : pas de _pruning_ de poids ou de neurones qui pourrait créer des réseaux sparses nécessitant des packages de traitement spécifiques, et nécessitant également une capacité de stockage importante pour garantir le soutien des structures _sparse_ ; ici on prune des filtres présentant de la redondance dans l'information, et on diminue le nombre de multiplications matricielles
  $\mathcal{l}_1$-norm est utilisée pour sélectionner les filtres à _prune_
* Problème : {{<cite "he2018">}} cite le problème de _covariate shift_ qualifiant la formation d'un biais au sein de notre apprentissage

<!--
[Han et al., 2015a] Song Han, Huizi Mao, and William J Dally. Deep compression: Compressing deep neural networks with pruning, trained quantization and huffman coding. In ICLR, 2015. 
[Han et al., 2015b] Song Han, Jeff Pool, John Tran, and William Dally. Learning both weights and connections for efficient neural network. In NIPS, 2015. 
[He et al., 2016a] Kaiming He, Xiangyu Zhang, Shaoqing Ren, and Jian Sun. Deep residual learning for image recognition. In CVPR, 2016. 
[He et al., 2016b] Kaiming He, Xiangyu Zhang, Shaoqing Ren, and Jian Sun. Identity mappings in deep residual networks. In ECCV, 2016
-->

Ici soft filter pruning {{<cite "he2018">}} : on continue de mettre à jour les _pruned filters_
Chaque interval (nombre d'époques), on calcule l'importance ($\mathcal{l}_2$-norm) de chaque filtre pour chaque couche $i$, et on met à zéro une part $P_i$ prédéfinie de filtres, puis on réapprend à l'époque suivante.   

<!--
[Guo et al., 2016] Yiwen Guo, Anbang Yao, and Yurong Chen. Dynamic network surgery for efficient DNNs. In NIPS, 2016.
-->



