---
layout: page
title: . Présentation et rapport intermédiaire
menu:
  main:
    weight: 10
bibFile: content/bibliography.json
toc: True
---

Cette section présente les éléments choisis ainsi que l'organisation du __rapport et de la présentation intermédiaire du stage__ se tenant à AgroParisTech (Palaiseau) le 4 juin 2024 de 9h45 à 10h25. Le projet Overleaf de ce rapport est accessible sur ce [lien](https://www.overleaf.com/read/sshbhhgmzksm#fa7d2e) et celui de la présentation sur ce [lien](https://www.overleaf.com/read/gbywzhjpfrgb#587646).

<!--more-->

## Consignes

Une __soutenance intermédiaire__ se déroule en juin devant un jury composé, au minimum, du maître de stage ou de son représentant et d'au moins deux enseignants de la DA IODAA. Les élèves remettent à cette occasion un rapport écrit (d'environ __15 pages__) et font une présentation orale de leur travail. Ce rapport et cette présentation sont notés sur la forme et permettent au jury d'apprécier le travail fait et à faire, d'orienter les élèves, au besoin de les réorienter.

Une __soutenance finale__ se déroulant normalement durant le mois de septembre, devant un jury composé au minimum de deux enseignants de la DA IODAA. Les élèves doivent remettre un rapport écrit (d'environ 20 à 40 pages) et font une présentation orale démarrant par une partie introductive en anglais. L'ensemble est alors jugé sur le fond et sur la forme. Une appréciation du travail réalisé et des qualités du stagiaire est demandée au maître de stage, oralement s'il est présent lors de la soutenance finale, par écrit sinon.

### Le rapport intermédiaire

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


## Plan détaillé du rapport intermédiaire

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