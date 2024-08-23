---
layout: page
title: . Planification du stage
menu:
  main:
    weight: 10
bibFile: content/bibliography.json
toc: True
mermaid: True
---

Planification du stage établie le 17/06/2024.

<!--more-->

# Déroulé du stage

Objectifs : identifier le problème de coûts écologique et du compromis performance de mmodèle/performance énergétiques dans le domaine de la télédétection, en prenant en compte ses spécificités.
Le stage s'organise selon 3 points, correspondant à 3 cas d'applications classiques en télédétection actuellement.

## Les traitement classiques en machine learning (traitement statistiques)

Effectuer des méthodes statistiques classiques comme celle appliquée dans l'onglet `I. Principe de détection de changement`.
La stratégie employée dans cette partie est l'application de telles méthodes statistiques sur des images SAR de $100 \times 100$. On identifiera et appliquera également d'autres algorithmes de détéction de changement sur des images SAR.
On réalisera des experiences selon plusieurs modalités de paramaètres du modèle (taille de la fenêtre à 5, 7, 21px), et on testera différents régimes pour l'algorithme (mono-thread, multi-thread). On testera également plusieurs implémentations (en C/Rust, Jax/Numba).

On mesurera les performances énergétique de ces modèles en récupérant les valeurs de consommation CPU, RAM, et de consommation énergétique mesurée sur la prise, sur lesquels on effectuera des traitements de réduction de bruit.

Soit $t_0$ un temps initial, $t_1$ le temps de début de mesure objective, $t_2$ le temps de fin de mesure objective, $t_l$ le temps de latence avant le process, $F(t)$ la consommation considérée. On calculera alors :

$$
\int_{t_1}^{t_2} F(t) - \left(\frac{1}{t_1-t_l-t_0}\int_{t_0}^{t_1-t_l} F(t) \mathrm{d}t\right) \mathrm{d}t
$$

On calculera également des métriques de performances (AUC, SSIM), permettant alors d'établir une analyse statistiques sur nos résultats et de lier les modalités énérgértiques aux modalités de performances.

## Les traitement classiques en apprentissage profond


## L'apprentissage semi-supervisé

# Agenda

{{<mermaid>}}
gantt
    dateFormat  YYYY-MM-DD
    title       Adding GANTT diagram functionality to mermaid
    excludes    weekends
    %% (`excludes` accepts specific dates in YYYY-MM-DD format, days of the week ("sunday") or "weekends", but not the word "weekdays".)

    section Partie I
    Réunion de planification :milestone, I1, 2024-06-17, 0d
    Implémentation de la récupération de consos   :I2, after I1, 1d
    Future task               :I3, after I1, 2d
    Documentation méthodes change detection :I4, after I1, 5d
    Fin de partie I :milestone, If, 2024-07-16, 0d

    section Critical tasks
    Completed task in the critical line :crit, done, 2014-01-06,24h
    Implement parser and jison          :crit, done, after des1, 2d
    Create tests for parser             :crit, active, 3d
    Future task in critical line        :crit, 5d
    Create tests for renderer           :2d
    Add to mermaid                      :until isadded
    Functionality added                 :milestone, isadded, 2014-01-25, 0d

    section Documentation
    Describe gantt syntax               :active, a1, after des1, 3d
    Add gantt diagram to demo page      :after a1  , 20h
    Add another diagram to demo page    :doc1, after a1  , 48h

    section Last section
    Describe gantt syntax               :after doc1, 3d
    Add gantt diagram to demo page      :20h
    Add another diagram to demo page    :48h

{{</mermaid>}}