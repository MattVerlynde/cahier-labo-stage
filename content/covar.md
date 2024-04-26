---
layout: page
title: VI. Bibliographie - Covariance et Optimisation
menu:
  main:
    weight: 6
bibFile: content/bibliography.json
toc: True
---

Notes de lectures de bibliographie sur les méthodes de covariance et d'optimisation.

<!--more-->

## Bibliographie

{{<cite "li2017">}} {{<cite "huang2016">}} {{<cite "boumal2023">}} 

## Questions

Sur l'article de {{<cite "li2017">}} :

* Quelles sont les questions scientifiques abordés par le papier et comment le papier y réponds-il ?

Objectif : augmenter la précision des modèles d'apprentissage profond, en se concentrant sur les statistiques utilisées plutôt que sur la tailled es réseaux de neurones, et proposer un modèle adapté au traitement de grandes masses de données (là où les modèles basés sur des statistques d'ordres 1sont performants sur des données de taille moyenne, moins sur des grandes).

Résponse proposée : utiliser des statistiques d'ordre 2 (covariance) pour améliorer la précision des modèles d'apprentissage profond, et donc proposer une estimation robuste de la matrice de covariance (en comparaison avec des méthodes existances basées sur la covariance). La méthode MPN-COV (__Matrix Power Normalized Covariance__) est proposée pour répondre à ces objectifs, ainsi qu'une méthode de rétropopagation du gradient associée.


* Quels sont les outils théoriques qu’ils ont besoin d’utiliser et pourquoi ils vont par là plutôt que d’utiliser d’autres méthodes ?

Méthode de rétropropagation du gradient matricielle {{<cite "ionescu2015">}} : elle permet de calculer les dérivées partielles de la fonction de coût avec des fonctions matricielles
Méthode d'estimation de covariance basée sur la vN-MLE (__von Neumann Maximum Likelihood Estimation__) : permet de diminuer le biais d'estimation surestimant les grandes valeurs propres et sous-estimant les valeurs basses de manière plus performante que la "simple" MLE.



* Quelles sont les résultats obtenus et avec quelle démarche méthodologique ?

Comparaison de la méthode suivant plusieurs valeurs de paramètres, 


* Qu’est ce qu’on peut critiquer/améliorer sur la démarche du papier ?