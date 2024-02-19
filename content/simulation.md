
---
layout: page
title: Un exemple de simulation numérique
menu:
  main:
    weight: 6
toc: False
---

Exemple de page détaillant un plan de simulation numérique et présentant les résultats associés.

<!--more-->

On va s'intéresser à vérifier l'aspect non-biaisé et l'efficacité des estimateurs de la moyenne et de la matrice de covariance dérivés à la page [suivante]({{< ref "calcul" >}}). Pour ce faire, on propose d'effectuer une simulation de type Monte-Carlo en générant des données aléatoires selon une loi normale multivariée de dimension $d$ et effectuer l'estimation de la moyenne et de la matrice de covariance. On pourra alors comparer les estimateurs obtenus avec les valeurs théoriques.


## Vérification de l'aspect non-biaisé

1. Paramètres de la simulation
On se place avec les paramètres suivants:
| Paramètre             | Valeur                                                                                                                                                |
|-----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| $d$                   | 2                                                                                                                                                     |
| $n$                   | 10, 12, 16, 20, 25, 32, 41, 52, 67, 85, 108, 137, 174, 221, 280, 356, 452, 573, 727, 923, 1172, 1487, 1887, 2395, 3039, 3856, 4893, 6210, 7880, 10000 |
| $\boldsymbol{\mu}$    | $[-30, 25]^T$                                                                                                                                         |
| $\boldsymbol{\Sigma}$ | $\begin{bmatrix}10&5\\\5&10\end{bmatrix}$
| Trials      | 10000       |

2. Lancer la simulation

Le script utilisé est disponible sur la machine `lst-pa33` au chemin: 
`/usr/shared/examples_simulations/montecarlo_2D_bias_scm.py`

Pour le lancer, on a besoin des packages suivants:
* Numpy
* Plotly
* Pandas

3. Résultats

Pour une simulation lancée le 08/02/2024 à 15h00:

<iframe src="../example_simulation/montecarlo_2D_bias_mean.html"
width="800" height="500" style="border: none;"></iframe>
<iframe src="../example_simulation/montecarlo_2D_bias_scm.html"
width="800" height="500" style="border: none;"></iframe>
