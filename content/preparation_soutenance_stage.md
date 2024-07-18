---
layout: page
title: . Présentation et rapport de stage
menu:
  main:
    weight: 11
bibFile: content/bibliography.json
toc: True
mermaid: True
---

Cette section présente les éléments choisis ainsi que l'organisation du __rapport et de la présentation intermédiaire du stage__ se tenant à AgroParisTech (Palaiseau) le 4 juin 2024 de 9h45 à 10h25. Le projet Overleaf de ce rapport est accessible sur ce [lien](https://www.overleaf.com/read/sshbhhgmzksm#fa7d2e) et celui de la présentation sur ce [lien](https://www.overleaf.com/read/gbywzhjpfrgb#587646).

<!--more-->

## Consignes

{{<unroll-block "Présentation intermédiaire">}}
Une __soutenance intermédiaire__ se déroule en juin devant un jury composé, au minimum, du maître de stage ou de son représentant et d'au moins deux enseignants de la DA IODAA. Les élèves remettent à cette occasion un rapport écrit (d'environ __15 pages__) et font une présentation orale de leur travail. Ce rapport et cette présentation sont notés sur la forme et permettent au jury d'apprécier le travail fait et à faire, d'orienter les élèves, au besoin de les réorienter.

La __soutenance orale dure 35mn__, dont __15 min__ pour l’exposé du sujet, du travail réalisé et des perspectives, et __20 min__ pour les questions et la discussion avec l’encadrant en entreprise. 

* __Le rapport intermédiaire__

Ce rapport sera évalué essentiellement sur la qualité de la définition de l'objectif du travail projeté, ses enjeux, ses limites, et sa position par rapport à l'état de l'art et par rapport aux compétences de l'organisme d'accueil. Le rapport intermédiaire doit permettre au jury d'aider l'élève à progresser correctement dans son travail. Le rapport doit indiquer l'état d'avancement des travaux, le plan et l'échéancier de la suite des travaux de manière détaillée de même que le travail accompli par le stagiaire en suivant la structure du rapport final (voir section suivante). Le corps du rapport intermédiaire pourra en effet (après corrections éventuelles à partir des remarques du jury) constituer une partie du rapport final.

Titre du stage: Rechercher un titre bref (éventuellement complété d'un sous-titre), centré sur un ou deux mots-clefs, caractérisant le principal apport scientifique ou technique attendu du stage.
Couverture et page de titre normalisée.

* Encadrement et environnement :

    Directeur du stage
    Localisation du bureau (téléphone) du directeur du stage et du stagiaire
    Description de l'établissement d'accueil, du service d'accueil et de la situation du second dans le premier.
    Personnes et organismes appelés à intervenir, contrats, moyens (langages informatiques, systèmes de traitement de texte...)

* Travail projeté :

    Présentation du sujet,
    Echéancier des travaux avec les dates absolues,
    Position dans l'organigramme de l'entreprise et du projet.

* Intérêt du travail :

    État actuel des connaissances scientifiques et techniques sur le sujet.
    Apport attendu du travail (création de concepts, de méthodologie, d'outils scientifiques ou techniques, résultats originaux, application dans un domaine nouveau d'outils connus, étude exploratoire...),
    Disciplines concernées,
    Intérêt par rapport à la formation à la DA IODAA et par rapport aux métiers d'ingénieur AgroParisTech.

* Bibliographie

* En annexe, travail déjà effectué, fiches de lectures...

{{</unroll-block>}}

{{<unroll-block "Présentation finale">}}

Une __soutenance finale__ se déroulant normalement durant le mois de septembre, devant un jury composé au minimum de deux enseignants de la DA IODAA. Les élèves doivent remettre un rapport écrit (d'environ 20 à 40 pages) et font une présentation orale démarrant par une partie introductive en anglais. L'ensemble est alors jugé sur le fond et sur la forme. Une appréciation du travail réalisé et des qualités du stagiaire est demandée au maître de stage, oralement s'il est présent lors de la soutenance finale, par écrit sinon.

Le __rapport final__ doit comporter :

* Une Page de remerciements indiquant clairement dans quelles conditions le travail a été effectué (établissement, laboratoire, directeur du laboratoire, directeur du stage, autres auteurs du mémoire, partie originale personnelle...).
* Un résumé en anglais (« abstract »).
* Un Résumé: il doit, en 2 pages maximum, contenir les indications suivantes :
    * Objet et limite du travail,
    * Méthodes choisies,
    * Apport scientifique et technique compte tenu de l'état antérieur des connaissances sur le sujet,
    * Résultats obtenus et développements ultérieurs possibles,
    * Applications dans l'entreprise,
    * Principales sources bibliographiques.
* une Table des matières.

Quant au contenu, le rapport final doit permettre au lecteur de se faire une opinion personnelle sur la validité du travail de développement, recherche et conduite de projet effectué et des conclusions auxquelles l'élève est parvenu. C'est pourquoi il doit être bref, précis, de présentation claire et dégager ce qui est original de ce qui vient de l’état de l’art existant. Destiné principalement à être lu par le jury de soutenance, et éventuellement par d'autres professionnels sous réserve qu'il n'y ait pas de clause de confidentialité, il pourra servir également dans la carrière de l'élève en démontrant ses qualités d'ingénieur.

{{</unroll-block>}}

## Présentation intermédiaire

{{<unroll-block "Plan détaillé du rapport intermédiaire">}}

* __Résumé__

* __I. Introduction__

    * __A. Encadrement et environnement__

        * __1. Organisme d'accueil__

        Présentation du laboratoire, organisation, thématiques ReGaRD et AFUTÉ

        * __2. Méthode de travail__

        Présentation du cahier de laboratoire, réunions régulières, matériel mis à disposition

    * __B. Contexte scientifique__

    Présentation courte des enjeux de la télédétection (domaines d'application, en lien avec les thèmes abordés en AFUTÉ), présentation de l'imagerie SAR

* __II. Projet__

    * __A. Présentation du projet__

    Objectifs initiaux du stage, comme présenté dans la proposition de stage, problématique

    * __B. Etat de l'art__

    Bibliographie sur les méthodes de calcul d'efficience computationnelle, méthodes de réduction d'architecture de réseaux de neurones, puis plus particulièrement sur les méthodes basées sur les statistiques de second ordre (covariance pooling)

    * __C. Résultats intermédiaires__

        * __1. Construction d'un référentiel méthodologique__

        Début du stage, détection de changement sur images SAR basé sur les estimations de covariances, rpise en main des données SAR 

        * __2. Enregistrement de données d'efficience computationnelle__
        
        Présentation de l'architecture Telegraf-InfluxDB-Grafana et de la connection Z-Wave-HomeAssistant (et MQTT ?), visualisation des consommations électriques

        * __3. Cas d'étude sur la base de données BigEarthNet__

        Présentation de la base de données, des architectures testées pour créer la baseline et des tests réalisés, résultats intermédiaires

    * __D. Planification future__

    Implémentation du covariance pooling, tests de performances électriques sur la base BigEarthNet, fusion d'inforamtion et choix d'une métrique de performance comprenant les performances calculatoires de la méthode et l'efficience énergétique.

* __III. Intérêt pour la formation AgroParisTech__

Démarche de projet construite, mise en situation sur des vraies données de consommation, prise en main de l'enjeu l'efficience énergétique (peu abordé au cours de la formation de 3e année, complémentarité avec la dominante Gestion et ingénierie de l'environnement en 2e année), intégration dans des dynamiques de laboratoire (séminaires, AG, recherches de financement)

* __IV. Conclusion__

* __Glossaire__

* __Bibliographie__

* __Annexes__

Fiche de proposition de stage, organigramme du laboratoire

{{<mermaid>}}
flowchart LR
    a4[Données de la
     prise intelligente] --> alpha{{Z-Wave}}
    a1[Données de la RAM] --> b{{Telegraf}}
    a2[Données du CPU] --> b{{Telegraf}}
    a3[Données du GPU] --> b{{Telegraf}}
    
    alpha{{Z-Wave}} --> beta{{MQTT Broker}}
    beta{{MQTT Broker}} --> b{{Telegraf}}
    b{{Telegraf}} --> c[(InfluxDB)]
    c[(InfluxDB)] --> d((Grafana))

    d ~~~ legend1[Conteneur
     Docker]
    d ~~~ legend2[Source de
     données]
    subgraph Legend[<u>Légende :</u>]
    legend1 ~~~ legend3[(Base de
     données)]
    legend2 ~~~ legend4((Visualiseur))
    end

    style alpha fill:#0db7ed,stroke:#384d54,stroke-width:2px,color:#384d54
    style b fill:#0db7ed,stroke:#384d54,stroke-width:2px,color:#384d54
    style beta fill:#0db7ed,stroke:#384d54,stroke-width:2px,color:#384d54
    style c fill:#0db7ed,stroke:#384d54,stroke-width:2px,color:#384d54
    style d fill:#0db7ed,stroke:#384d54,stroke-width:2px,color:#384d54
    
    style a1 stroke:#384d54,stroke-width:2px,color:#384d54
    style a2 stroke:#384d54,stroke-width:2px,color:#384d54
    style a3 stroke:#384d54,stroke-width:2px,color:#384d54
    style a4 stroke:#384d54,stroke-width:2px,color:#384d54

    style Legend fill:#fff,stroke-width:0px,color:#384d54
    style legend1 fill:#0db7ed,stroke-width:0px,color:#384d54
    style legend2 stroke-width:0px,color:#384d54
    style legend3 fill:#fff,stroke:#384d54,stroke-width:2px,color:#384d54
    style legend4 fill:#fff,stroke:#384d54,stroke-width:2px,color:#384d54
    
{{</mermaid>}}

{{</unroll-block>}}

{{<unroll-block "Plan détaillé de la présentation intermédiaire">}}

* __Résumé__ 

* __Introduction__

    * __A. Laboratoire d'Informatique, Système et Traitement de l'Information et de la Connaissance__

        Présentation du laboratoire, organisation, thématiques ReGaRD et AFUTÉ

    * __B. La télédétection : gestion de l'environnement aux coûts écologiques croissants__

    Présentation courte des enjeux de la télédétection (domaines d'application, en lien avec les thèmes abordés en AFUTÉ), présentation de l'imagerie SAR, et du constat d'augmentation de la consommation énergétique dans le domaine

* __I. Optimiser les algorithmes de traitement d'images satellites__

    * __A. Des méthodes d'optimisations variées__

    Bibliographie sur les méthodes de réduction de consommation énergétique et computationnelle de réseaux de neurones : pruning, 

    * __B. Métriques d'évaluation__

    Méthodes de calcul d'efficience computationnelle, de compléxité d'algorithme, et d'efficience énergétique

* __II. Travaux effectués__

    * __Enregistrement de données d'efficience computationnelle__
        
        Présentation de l'architecture Telegraf-InfluxDB-Grafana et de la connection Z-Wave-HomeAssistant (et MQTT ?), visualisation des consommations électriques

    * __Cas d'étude sur la base de données BigEarthNet__

        Présentation de la base de données, des architectures testées pour créer la baseline et des tests réalisés, résultats intermédiaires

* __III. Organisation de la suite du stage__

Implémentation du covariance pooling, tests de performances électriques sur la base BigEarthNet, fusion d'inforamtion et choix d'une métrique de performance comprenant les performances calculatoires de la méthode et l'efficience énergétique.

* __Conclusion__

Démarche de projet construite, mise en situation sur des vraies données de consommation, prise en main de l'enjeu l'efficience énergétique (peu abordé au cours de la formation de 3e année, complémentarité avec la dominante Gestion et ingénierie de l'environnement en 2e année), intégration dans des dynamiques de laboratoire (séminaires, AG, recherches de financement)

{{</unroll-block>}}

{{<mermaid>}}
mindmap
  root((Accélération 
  du CNN))
    Structure du réseau
        Blocs spécifiques
        Apprentissage par renforcement
        Statistiques utilisées
        ...

    Optimisation du réseau
        Décomposition de matrice
        Pruning
        Quantization
        ...

    Hardware
      Plateforme de calcul
        CPU
        GPU
        ...

      Optimisation
        Réutilisation de calcul
        Optimisation de mémoire
        ...
{{</mermaid>}}

