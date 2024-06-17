---
layout: page
title: I. Principe de détection de changement
menu:
  main:
    weight: 1
bibFile: content/bibliography.json
toc: True
---

Test d'un algorithme de détection de changement.
Ce problème est centré sur l'optimisation d'algorithme de détection de changements à partir d'images SAR (Radar à synthèse d'ouverture).

<!--more-->

## Introduction

Ce test d'algorithmes de détection de changement basé sur les travaux de {{<cite "conradsen2016">}} et {{<cite "mian2019">}}.

## Données

Les données à notre disposition afin de réaliser nos tests sont:
* Images SAR au format `.npy`
    * Scene 1 avec une taille 2360x600x3 sur 4 dates
    * Scene 2 avec une taille 2360x600x3 sur 4 dates
    * Scene 3 avec une taille 3000x1500x3 sur 17 dates
    * Scene 4 avec une taille 3000x3000x3 sur 68 dates
  
  Chaque image présente 3 bandes correspondant à 3 types de signaux : HH (onde émise horizontale et reçue horizontale), HV (onde émise horizontale et reçue verticale) et VV (onde émise verticale et reçue verticale).
* Images présentant une vérité terrain au format `.npy` pour les scènes 1 à 3 

<iframe src="../ground_truth/ground_truth_scene_1.html"
width="400" height="400" style="border: none;"></iframe>

<iframe src="../ground_truth/ground_truth_scene_2.html"
width="400" height="400" style="border: none;"></iframe>

<iframe src="../ground_truth/ground_truth_scene_3_temporal_4_classes.html"
width="400" height="400" style="border: none;"></iframe>
  
  Ces images permettent de quantifier la performance des préditions réalisées par nos algorithmes.

## Présentation de l'algorithme global

Pour étudier ce problème, nous nous basons sur l'algorithme de {{<cite "conradsen2016">}} afin de détecter les pixels présentant au moins un changement au cours de la série temporelle, le nombre et la localisation temporelle des changements.

Pour cela, les comparaisons sont réalisées entre les matrices de variance-covariance des trois bandes pour chaque pixel à chaque temps.

### Détection des zones de changement

Notons $T$ le nombre d'images, 

La matrice de covariances calculée pour un pixel à un temps $t \in \[[1, T\]]$ notée $\Sigma_t$ est estimée pour un nombre de pixels $k \in \[[1, N\]]$. Celle-ci est calculée de façon suivante $\Sigma_t = \mathbb{E} ( x_k^t,{x_k^t}^H ) $ (où $\bullet^H$ correspond à l'opérateur transposé conjugué).

Nous testons d'abord si la série temporelle présente ou non au moins un changement pour chaque pixel, donc si l'ensemble des $\Sigma_t$ pour $t \in \[[1, T\]]$ ont la même espérance $\Sigma$, ou si leurs espérences sont différentes, soit qu'il existe $\(i,j\) \in \[[1, T\]]^2$ tel que $i \neq j$ et $\Sigma_i \neq \Sigma_j$

Nous considérons donc des variables aléatoires indépendantes $X_i, i = 1, . . . , T$ suivant une distribution de Wishart complexe.

$$
X_i \sim W_C(p, n, \Sigma_i), i = 1, \cdots , T
$$

où $\mathbb{E}(X_i/n) = \Sigma_i$. Ceci nous permet de tester l'hypothèse nulle

$$
H_0 : \Sigma_1 = \Sigma_2 = \cdots = \Sigma_T
$$

Nous posons alors

$$
Q = T^{pnT}\frac{\prod_{i=1}^{T}|X_i|^n}{|X|^T}
$$

où $|\bullet|$ qualifie le déterminant, $X_i \sim W_C(p, n, \Sigma_i)$ et $X = \sum_{i=1}^{T}X_i \sim W_C(p, nT, \Sigma)$, p est le nombre de bandes (ici 3) et n le paramètre ENL (_Equivalent Number of Looks_, correspondant au nombre de pixels dans la fenêtre de calcul de la covariance pour chaque pixel). 

Pour le logarithme du test statistique, on obtient:

$$
\ln Q = n \left( pT \ln T + \sum_{i=1}^{T}\ln|X_i|-T\ln|X| \right)
$$

{{<highlight-block "Proposition" >}}
Soient :
$$ 
f = \(k − 1\)p^2
$$ 
$$ 
\rho = 1 - \frac{2p^2-1}{6\(T-1\)p}\(\frac{T}{n}-\frac{1}{nT}\)
$$ 
$$
\omega_2 = \frac{p^2\(p^2-1\)}{24\rho^2}\left(\frac{k}{n^2}-\frac{1}{(nT)^2}\right)-\frac{p^2\(k-1\)}{4}\left(1-\frac{1}{\rho}\right)
$$

La probabilité de trouver une plus petite valeur de $-2 \rho \ln Q$ est 
$$
P \left( −2\rho \ln Q \leq z \right) \approx P \left( \chi^2 (f) \leq z \right)  + \omega_2 \left[ P \left( \chi^2(f + 4) \leq z \right) − P \left( \chi^2(f) \leq z \right) \right]
$$

avec $z = -2 \rho \ln q$ et $q$ est une valeur observée de la variable aléatoire $Q$.

{{</highlight-block>}}

{{<proof>}}
To add
{{</proof>}}

{{<highlight-block "Proposition" >}}

Pour tester l'hypothèse, avec $j \in \[[1,T\]]$

$$
H_{0,j} : \Sigma_{j-1} = \Sigma_j 
$$
$$
H_{1,j} : \Sigma_{j-1} \neq \Sigma_j
$$

Posons la statistique de test $R_j$ telle que :

$$
R_j = \left[ \frac{j^{jp}}{(j-1)^{(j-1)p}} \frac{|X_1 + \cdots + X_{j-1}|^{(j-1)n}|X_j|^n}{|X_1 + \cdots + X_j|^j} \right]^n
$$

et nous avons

$$
Q = \prod_{j=2}^T R_j
$$

{{</highlight-block>}}

{{<proof>}}
To add
{{</proof>}}

### Détection des évènements de changement

$$
H_{0} : \forall (i,j) \in \mathbb{N}^2, \Sigma_i = \Sigma_j 
$$
$$
H_{1} : \exists (i,j) \in \mathbb{N}^2, \Sigma_i \neq \Sigma_j
$$

$$
H_{0,j} : \Sigma_{j-1} = \Sigma_j 
$$
$$
H_{1,j} : \Sigma_{j-1} \neq \Sigma_j
$$

{{<pseudocode>}}

\begin{algorithm}

\caption{Change detection}

\begin{algorithmic}

    \STATE Initialize list of change points $\mathcal{L}$

    \STATE $\ell = 1$

    \While{$H_0^{(\ell)}$ not accepted}

        \STATE Set $\mathcal{j} = 2$

        \While{$H_{0,\mathcal{j}}^{(\ell)}$ accepted}

            \STATE $\mathcal{j} = \mathcal{j}+1$

        \EndWhile

        \STATE $\ell = \ell+\mathcal{j}$

        \STATE Append $\ell$ to $\mathcal{L}$

    \EndWhile

\end{algorithmic}

\end{algorithm}

{{</pseudocode>}}


## Résultats

Application sur les données de test :

* Calculs en parallèle des matrices de covariances et de la statistique de test pour chaque pixel (à l'aide d'une fenêtre glissante réalisée par la méthode `sliding_window_view` de `numpy` ainsi que du package `Joblib`).

  <iframe src="../results/Scene_1_lnq.html" width="450" height="450" style="border: none;"></iframe>

  <iframe src="../results/Scene_2_lnq.html" width="450" height="450" style="border: none;"></iframe>

  <iframe src="../results/Scene_3_lnq.html" width="450" height="450" style="border: none;"></iframe>

  Le script utilisé est disponible sur la machine `lst-pa33` au chemin: `verlyndem/static/scripts/cd_sklearn_pair.py`.

Les performances des différentes contructions du code d'exécution de cet algorithme sont présentées dans le tableau suivant, en quantifiant la durée d'éxecution du code (en minutes).

|Construction de l'algorithme                            | Scene 1 | Scene 2 | Scene 3 | Scene 4 |
|--------------------------------------------------------|:-------:|:-------:|:-------:|:-------:|
| Sans parallélisation                                   |  2:14   |  2:15   |    x    |    x    |
| Avec fenêtre glissante et parallélisation sur `Joblib` |  0:40   |  0:36   |    x    |    x    |
| Avec traitement des images paire à paire               |         |         |  12:02  |         |

{{<error>}}
Un problème au niveau de nos données rend l'application de nos tests inutilisable : l'hypothèse de distribution gaussienne de la variable aléatoire de test. Celle-ci n'est pas vérifiée dans notre cas. 
{{</error>}}

Testons sur notre algorithme sur un échantillon de données jouet, aux valeurs artificiellement distribuées de façon gaussienne. Nous fixons pour ces simulations le paramètre $\text{window_size} = 21$.

Dans le cas de ma première image jouet, deux zones ont été délimitées avec un même changement à deux temps différents. 

<iframe src="../results/custom_test_image_n22500_T4_p3_3_truth.html"
width="400" height="400" style="border: none;"></iframe>

La première zone présente un changement au 4e temps, et la seconde présente un changement au deuxième temps.  

<iframe src="../results/custom_test_image_n22500_T4_p3_3_lnq.html"
width="300" height="300" style="border: none;"></iframe>
<iframe src="../results/custom_test_image_n22500_T4_p3_3_pvalue.html"
width="300" height="300" style="border: none;"></iframe>
<iframe src="../results/custom_test_image_n22500_T4_p3_3_pvalue_threshold.html"
width="300" height="300" style="border: none;"></iframe>

Les zones de changement sont bien détectées par l'algorithme, mais également des zones supplémentaires. 

Dans le cas de la seconde image jouet, les changements sont éparses sur la surface d'étude.

<iframe src="../results/custom_test_image_n22500_T4_p3_truth.html"
width="400" height="400" style="border: none;"></iframe>

Les zones de changement correspondent à des variations de covariance à des temps aléatoires entre le second et le quatrième temps.

<iframe src="../results/custom_test_image_n22500_T4_p3_lnq.html"
width="300" height="300" style="border: none;"></iframe>
<iframe src="../results/custom_test_image_n22500_T4_p3_pvalue.html"
width="300" height="300" style="border: none;"></iframe>
<iframe src="../results/custom_test_image_n22500_T4_p3_pvalue_threshold.html"
width="300" height="300" style="border: none;"></iframe>  

L'algorithme possède donc plus de difficulté pour identifier les zones de changement, ceci dû à l'étape de calcul des covariances.
