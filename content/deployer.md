---
layout: page
title: Déployer le cahier sur le serveur du laboratoire
menu:
  main:
    weight: 2
toc: true
---

Informations sur le déploiement du cahier sur le serveur du laboratoire une fois généré en local.

<!--more-->

## Pré-réquis

### VPN

Le serveur n'est accessible qu'à travers le VPN de l'université ou bien à travers une connexion fiilaire sur les ordinateurs au laboratoire.

Pour ce qui est du VPN, il faut un compte à l'université puis suivre la procédure à l'adresse: [vpn.univ-smb.fr](vpn.univ-smb.fr).

### Accès au serveur

Le serveur est disponible à l'adresse: `lst-pa33.local.univ-savoie.fr`. Pour accéder aux cahiers déjà compilés, se rendre au port `8000`: `http://lst-pa33.local.univ-savoie.fr:8000/`.

Pour pouvoir mettre à jour son cahier, il faut un compte sur la machine qui a dû être crée à votre arrivée. Demander à [Ammar](mailto:ammar.mian@univ-smb.fr?subject=[Cahier de laboratoire]) au besoin.

## Mise à jour du cahier

### Création du dossier sur le serveur

Si vtre dossier (correspondant à votre nom utilisateur sur la machine) n'existe pas dans l'arborescence de fichiers, le créer manuellement à partir d'une connexion ssh:

```console
ssh <nom_utilisateur>@lst-pa33.local.univ-savoie.fr
```
Votre mot de passe vous sera demandé.

{{<warning >}}
Ce serveur n'est pas destiné au lancement de calculs, sauf accord au préalable. Se conencter uniquement pour mettre à jour le cahier.
{{</warning>}}

Une fois connecté, se rendre au dossier `/usr/shared/lab_notebooks` et créer votre dossier:

```console
cd /usr/shared/lab_notebooks
mkdir <nom_utilisateur>
```

On peut enfin quitter la connexion ssh en faisant simplement `exit`.

### Mise à jour du cahier

Une fois votre cahier compilé sur votre machine dans le dossier `public` relatif à l'origine du cahier à l'aide de `hugo`, mettre à jour sur le serveur à l'aide de:

```console
scp -r public/. <nom_utilisateur>@lst-pa33.local.univ-savoie.fr:/usr/shared/lab_notebooks/<nom_utilisateur>
```

Votre mot de passe vous sera demandé et vous verez les fichiers se copier. Le cahier à l'adresse `lst-pa33.local.univ-savoie.fr:8000/<nom_utilisateur>` sera mis à jour.
