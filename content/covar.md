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

  * Comparaison des taux d'erreur de type top-1 de la méthode suivant plusieurs valeurs du paramètre $\alpha$ (exposant de la matrice de puissance), implémenté sur AlexNet
  
    Le taux d'erreur de type top-1 correspond à la proportion du temps où le classifieur donne la probabilité la plus élevée à la mauvaise classe. Le taux d'erreur de type top-5 correspond à la proportion du temps où la classe réelle ne fait pas partie des 5 classes prédites avec la plus grande probabilité par prédiction.

    Le paramètre $\alpha$ montre une diminution du taux d'erreur top-1 lorsque le paramètre est compris entre 0 et 1. Cette diminution est plus importante autour de $\alpha = 0.5$, mais tend à être moins importante lorsque $\alpha$ diminue en dessous de $0.5$.
  
  
  * Comparaison des taux d'erreurs de type top-1 et top-5 avec d'autres méthodes de normalisation (M-Fro et M-l2) implémenté sur AlexNet,
  
    
  
  * Comparaison des taux d'erreurs de type top-1 et top-5 avec d'autres méthodes basées sur des statistiques d'ordre 2 (B-CNN et DeepO<sub>2</sub>P)



  * Aussi implémentation dans des réseaux de neurones classiques (VGG-M, VGG-16, ResNet-50) et comparaison avec des taux d'erreurs avec et sans implémentation de la méthode, et avec d'autres réseaux classiques (PreLU-net B, GoogleNet)


* Qu’est ce qu’on peut critiquer/améliorer sur la démarche du papier ?