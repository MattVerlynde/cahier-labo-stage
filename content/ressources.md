
---
layout: page
title: Ressources au laboratoire
menu:
  main:
weight: 4
toc: True 
---

Liste de ressources utiles au laboratoire LISTIC.

<!--more-->

## Se connecter aux serveurs du laboratoire

Pour se connecter aux serveurs, plusieurs façons sont possibles. Dans tous les cas si vous êtes en dehors de la fac, il faut installer le VPN de l'USMB disponible [ici](https://vpn.univ-smb.fr/) et s'y connecter.

**1. Accès via `ssh`:**
Si vous voulez vous connecter sur la machine comme si vous y étiez on utilise le protocole `ssh. Dans un terminal, tapez la commande suivante:
```console
ssh <votre_login>@<adresse_ip> [options]
```
où,
* `<votre_login>` est votre login sur la machine
* `<adresse_ip>` est l'adresse IP de la machine, il peut être remplaçé par le nom de la machine sur le réseau.
* `[options]` sont des options pour la connexion, par exemple `-X` pour activer le forwarding de X11. Cela permet d'afficher des fenêtres graphiques sur votre machine locale.

**2. Accès via `sftp` ou `sshfs`:**
Si l'on veut juste accéder aux fichiers stockés sur la machine, on peut monter la machine comme un lecteur réseau:
* Sur Windows, on peut utiliser le logiciel [WinSCP](https://winscp.net/eng/index.php) pour se connecter en SFTP. Un autre outil utile est [sshfs-win](https://github.com/winfsp/sshfs-win)
* Sur Linux, on peut utiliser la commande `sshfs` pour monter la machine dans le système de fichier. Par exemple:
```console
sshfs <votre_login>@<adresse_ip>:/chemin/vers/le/dossier /chemin/vers/le/dossier/monté
```
* Sur macOS, on peut utiliser [FUSE for macOS](https://osxfuse.github.io/) pour avoir accès à `sshfs`. Il suffit alors de suivre les instructions pour Linux.

## Machine des cahier de laboratoire

Pour pouvoir rationaliser le stockage et l'accès aux différents cahiers de laboratoires de chacun, une machine à été choisie pour stocker les différentes pages de chacun. Les informations sont les suivantes:

Adresse | Utilisateur | Mot de passe
--- | --- | --
lst-pa33.local.univ-savoie.fr | <votre_login> | <votre_mot_de_passe>

Au début de votre stage, votre login et mot de passe vous seront communiqués. Les cahiers de laboratoires seront alors à stocker sur `/usr/shared/lab_notebooks`. Il suffit alors de créer un dossier avec votre nom et d'y stocker vos cahiers de laboratoire une fois compilés.

De plus, un serveur local permettra d'accéder à partir du vpn, aux pages à l'adresse:
`lst-pa33.local.univ-savoie.fr:8000/<votre_nom>/<nom_cahier>/`

A titre d'exemple vous devriez avoir accès à ce présent guide à l'adresse: `lst-pa33.local.univ-savoie.fr:8000/template/`.

## Ressources de calcul

Afin de pouvoir effectur des calculs qui demandent beaucoup de ressources, deux types de ressources sont disponobles:
* Les machines du laboratoire, qui sont des serveurs sous Linux qui ne possèdent pas de système de gestion de tâches,
* Le cluster de calcul de l'USMB **MUST**, qui est un ensemble de machines qui possèdent un système de gestion de tâches. Ce cluster possède un nombre significativment plus grand de ressources que les machines du laboratoire donc à privilégier pour de grands calculs. Toutefois, il demande plus de travail pour organiser ses simulations pour être lancées.

### Machines au labo

Le tableau suivant donne les informations sur les machines du laboratoire:
| Identifiant   | Salle | Monitor | OS installé     | Responsable        | IP              |
|---------------|-------|---------|-----------------|--------------------|-----------------|
| Pe176         | a123  | X       | Ubuntu22.04     | hermann.courteille | 193.48.124.176  |
| Dragibus      | seb   |         | Ubuntu20.04     | seb                | 192.168.143.28  |
| Renaissance   | a106  | X       | Ubuntu20.04     | nicolas.méger      | 192.168.143.70  |
| Pa52          | a106  | X       | Debian10 buster | alexandre.benoit   | 192.168.143.52  |
| Pa49          | abdou |         | Windows 10      | Abdou ...          | 192.168.143.49  |
| Pa16          | a106  |         | Ubuntu 14.04    | nicolas.méger      | 192.168.143.16  |
| Ubuntu1       | b204  | X       | Ubuntu20.04     |                    | 192.168.143.95  |
| Ubuntu2       | b204  | X       | Ubuntu20.04     |                    | 192.168.143.96  |
| WS3           | a106  |         | Windows 10      | Abdou ...          | 192.168.143.51  |
| WS2           | a106  | X       | Ubuntu20.04     | ?                  | 192.168.143.86  |
| MASTODON      | a106  | X       | Ubuntu20.04     | flavien            | 192.168.143.20  |
| WS1           | a106  | X       | Ubuntu20.04     | ?                  | 192.168.143.63  |
| VIP-MontBlanc | a106  |         | debian          | flavien            | 192.168.143.175 |

avec les ressources suivantes:
| Identifiant        | CPU (GHz) | CPU N-core | RAM (Go) | GPU card       | GPU (Go) | HD (To) hors OS | SSD (To) hors OS |
|--------------------|-----------|------------|----------|----------------|----------|-----------------|------------------|
| Pe176              | 3.6       | 12         | 18       | Quadro M5000   | 8        |                 |                  |
| Dragibus           | 3.8       | 16         | 256      | Quadro P400    | 2        | 0.5             | 0.12             |
| Renaissance        | 2.2       | 128        | 256      | RTX 2060 Super | 8        | 7               | 2                |
| Pa52               | 2.4       | 32         | 128      | 2 Titan XP     | 2 X 12   | 18              | 1.5              |
| Pa49               | 3.4       | 12         | 256      | K6000          | 12       | 2.5             |                  |
| Pa16               | 3.7       | 8          | 256      | Quadro K1200   | 4        |                 | 2                |
| ubuntu1            | 2.4       | 48         | 192      | A6000          | 48       | x               | x                |
| ubuntu2            | 2.4       | 48         | 192      | A6000          | 48       |                 |                  |
| WS3                | 3.2       | 16         | 256      | 2 X A6000      | 2 X 48   |                 |                  |
| Total Listic (GPU) |           | 320        | 1810     |                | 250      | 28              | 5.62             |
| WS2                | 2.2       | 96         | 128      | X              | X        | X               | X                |
| MASTODON           | 2         | 32         | 64       | X              | X        | 1.8             | 32               |
| WS1                | 2.2       | 96         | 128      | X              | X        | X               | X                |
| VIP-MontBlanc      | 2.3       | 20         | 64       | X              | X        | 4.1             |                  |
| Total Listic (CPU) |           | 244        | 384      |                |          | 5.9             | 32               |

Pour avoir accès à une de ces machines, il faut demander un accès au responsable ou bien à [Florent Baldini](mailto:florent.baldini@univ-smb.fr).

### MUST

La documentaiton du centre est disponible [ici](https://doc.must-datacentre.fr). Pour avoir un accès, il faut remplir un formulaire disponible [ici](https://doc.must-datacentre.fr/access/account/).

Le système gestion de tâches est HTCondor dont la documentation est disponible [ici](https://htcondor.readthedocs.io/_/downloads/en/v9_0/pdf/) pour la version installée sur MUST. Une meilleure doc sur la dernière version est disponible [ici](https://htcondor.readthedocs.io/en/latest/) et partage beaucoup de choses (Il faut juste faire attention dés fois que les noms soient différents mais c'est plûtot pour les fonctions avancées).

### Jean-Zay

Sur certains projets, il est possible d'avoir accès au supercalculateur Jean-Zay. Pour plus d'informations, se référer au [site web](http://www.idris.fr/jean-zay/jean-zay-presentation.html) et demander à Ammar Mian.

## Stockage de données

Pour stocker des données volumineuses, un NAS de stockage est disponible à l'adresse `193.48.124.184`. Il possède environ 144 To d'espace disque. Pour avoir accès à ce NAS, il faut demander un accès à [Florent Baldini](mailto:florent.baldini@univ-smb.fr). 

Une fois un compte crée, on peut monter celui soit en sshfs comme présenté plus haut ou en utlisant:

```console
sudo mount -t cifs -o username=identifiant,uid=NumUid,gid=NumGid //193.48.124.184/share dossierVide
```
