---
layout: page
title: II. Bibliographie - Mesures de performance d'algorithme
menu:
  main:
    weight: 2
bibFile: content/bibliography.json
toc: True
---

La durée d'exécution d'un programme peut rendre compte de l'optimalité d'un programme, mais elle peut apparaître insuffisante en cela qu'elle peut ne pas rendre commpte de la complexité intrinsèque de l'algorithme, et de la quantité d'énergie nécessaire (même si elle reste très corrélée à ces aspects) 

<!--more-->

## Durée d'exécution

Seule, elle est insuffisante pour rendre compte de la performance énergétique  {{<cite "nurminen2003">}} mais elle reste très correlée {{<cite "tomofumi2014">}}.

## Complexité

Mathématiquement, la complexité dans le pire cas (notation $\mathcal{O}(\bullet)$).

Software complexity measures {{<cite "nurminen2003">}} :
* Lines of code ($LOCpro$) : le nombre de lignes du code (mais très lié au style de codage),
* Volume d'Halstead ($V$) : nombre d'opérations et d'opérants, donc le nombre de bits du coeur de l'algorithme,
* Nombre cyclomatique $V(G)$ : pour une fonction, égal au $(\text{nombre de branches}) - 1$ et augmentant avec le nombre d'opérants _and_ et _or_.

## Energie

Nvidia GPUs energy consumption en utilisant la librarie `pynvml` ou l'utlisation d'une prise intelligente mesurant la consommation éléctrique de la machine en temps réel.

$\text{EDP (Energy Delay Product)} = \text{Energy} \times \text{Runtime}$

Pour comparer deux programmes : Greenup-Powerup-Speedup (GPS-UP) {{<cite "abdulsalam2015">}}
* $\text{Greenup} = G = \frac{E_{base}}{E_{optim}}$
* $\text{Powerup} = P = \frac{P_{base}}{P_{optim}}$
* $\text{Speedup} = S = \frac{T_{base}}{T_{optim}}$

et on a $G = S \times P$. Cependant,<span style="font-weight: bold; color: #ffffff; background-color: red"> au moins deux </span> de ces ratios doivent être utilisés pour apporter une information pertinente sur la comparaison de l'optimalité.

## Emission de GES (eqCO<sub>2</sub>)

Utilisation de packages précontruits (Carbon tracker {{<cite "lasse2020">}} ou CodeCarbon ([lien GitHub](https://github.com/mlco2/codecarbon)) par exemple) ou calcul basé sur nos mesures de consommation électrique.

Soit :

* $C$ = Emission en équivalent carbone (eqCO<sub>2</sub>)
* $E$ = Consommation énergétique (kWh)
* $I$ = Intensité carbone au lieu de consommation (eqCO<sub>2</sub>/kWh)

$C = E \times I$