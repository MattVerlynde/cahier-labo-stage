# Template pour cahier de laboratoire

Ceci est un projet permettant générer un cahier de laboratoire à partir de mardown. Ce cahier a été créé dans le cadre du satge de fin d'étude de Matthieu Verlynde au LISTIC à Annecy, supervisé par Dr. Ammar Mian. Ce cachier est basé sur un template créé par Dr. Ammar Mian 

Une démo en ligne du template est disponible [ici](https://ammarmian.github.io/template_cahierlabo/).

### Hugo

Ce template nécessite l'utilisation de [Hugo](https://gohugo.io/). Pour installer Hugo, veuillez suivre les instructions sur le site web.

### Génération du cahier

Pour générer le cahier, il suffit de cloner le projet et de lancer Hugo.
```console
hugo
```
Le dossier `public` contiendra les fichiers générés.

Afin de travailler en mode développement et voir les changements en temps réel, on peut lancer:
```console
hugo server --port <numéro de port>
```
où `<numéro de port>` est le port que vous souhaitez utiliser. Le site web sera ainsi disponible à l'adresse `http://localhost:<numéro de port>` et se mettra à jour à chaque changement dans les fichiers source.

## Utilisation du template

Un guide pour modifier le template est disponible en compilant le code sur ce projet [ici](https://github.com/AmmarMian/template_cahierlabo). Une dépendence à hugo-cite est donnée sous forme de submodule. Ainsi pour obtenir tout le code, lancer:

```console
git clone https://github.com/AmmarMian/template_cahierlabo --recurse-submodules
```

## Auteur

Matthieu Verlynde
* mail: [matthieu.verlynde@univ-smb.fr](mailto:matthieu.verlynde@univ-smb.fr)

Le template utilisé est fortement basé sur la base du template [Hyde](https://github.com/spf13/hyde).

Ammar Mian
* Web: [http://ammarmian.github.io](http://ammarmian.github.io)
* mail: [ammar.mian@univ-smb.fr](mailto:ammar.mian@univ-smb.fr)

Copyright @Université Savoie Mont Blanc, 2024

