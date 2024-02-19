
---
layout: page
title: Guide pour ajouter du contenu
menu:
  main:
    weight: 1
toc: true
---

Dans cette page, nous montrons comment sont organisés les fichiers relatifs à ce site web et comment ajouter de nouveau contenus et regénérer le site web.

<!--more-->

## Hugo

<img src="https://gohugo.io/img/hugo-logo.png" alt="Hugo" width="200"/>

>Hugo est un générateur de site web statique. Il permet de créer des sites web sans avoir à écrire du code HTML, CSS ou JavaScript. Il permet de créer des sites web rapidement et facilement. Pour plus d'informations sur Hugo, consultez [le site web officiel](https://gohugo.io/).

L'idée de base est qu'à partir d'un template déjà fait (comme pour le présent), il est possible de générer rapidement de nouvelles pages web en utilisant des fichiers markdown. Le mardown étant un langage de balisage léger, il est facile à écrire et à lire (voir [ici](https://fr.wikipedia.org/wiki/Markdown)).

### Installation de Hugo

Pour pouvoir compiler le cahier de laboratoire, il est nécessaire d'installer l'outil en local. Une page dédiée à cette installation est disponible [ici](https://gohugo.io/getting-started/installing/).

Toutefois, en résumé:
* Sur macOS, il est possible d'installer Hugo via Homebrew: `brew install hugo`
* Sur Windows, le plus simple reste de télécharger le programme précompilé depuis [cette page](https://github.com/gohugoio/hugo/releases/download/v0.122.0/hugo_0.122.0_windows-amd64.zip) et de placer le fichier 'hugo.exe' dans la racine du cahier de laboratoire.
* Sur Linux, il est possible d'installer Hugo via Snap: `snap install hugo`

### Générer le site web

* Sur macOS et Linux, il suffit de se placer dans le répertoire du cahier de laboratoire et de lancer la commande `hugo`. Cela va générer le site web dans le répertoire `public`.
* Sur Windows, à condition d'avoir placé le fichier 'hugo.exe' dans la racine du cahier de laboratoire, il suffit de lancer la commande `hugo.exe`. Cela va générer le site web dans le répertoire `public`.

Dans tous les cas, pour avoir une vue en temps réelle des modifications il est possible de lancer dans le terminal: `hugo server`. Cela va lancer un serveur local qui permet de voir les modifications en temps réel à l'adresse `http://localhost:1313/`.


## Organisation du template de cahier de laboratoire

Si l'on regade le contenu du répertoire du cahier de laboratoire, on trouve les fichiers suivants:
```console
compile.sh
config.toml
content/
README.md
static/
themes/
view.sh
```

* `compile.sh` et `view.sh` sont des scripts pour générer le site web et pour le visualiser en local.
* `config.toml` est le fichier de configuration de Hugo. C'est dans celui-ci que l'on définit le titre du site web, sa description, les menus, etc.
* `content` est le répertoire qui contient les fichiers markdown qui correspondent aux différentes pages du site web. Il y a une page par fichier markdown.
* `static` est le répertoire qui contient les fichiers statiques du site web (images, fichiers pdf, etc.). C'est ici que l'on va stocker les figures, résultats, etc
* `themes` est le répertoire qui contient le thème de Hugo. C'est ici que l'on peut modifier l'apparence du site web. A priori, il n'est pas nécessaire de modifier ce répertoire. Il faut toutefois ne pas le supprimer.

## Ajouter une nouvelle page

Pour ajouter une nouvelle page, il suffit de créer un nouveau fichier dans le dossier `content` et de le remplir avec du contenu en markdown. Par exemple, pour ajouter une page nommée `nouvelle_page.md`, il suffit de créer un fichier `nouvelle_page.md` dans le dossier `content` et de le remplir avec du contenu en markdown:
```markdown

---
layout: page
title: Une
menu:
  main:
    weight: 3
toc: true
---
Résumé
<!--more-->

Contenu de la page
```

Il ya quelques éléments à noter:
* L'en-tête entre `---` est nécessaire pour indiquer à Hugo que c'est une page. Il est possible de spécifier le layout de la page, le titre, le menu (dans quel ordre il apparait), etc.
* `<!--more-->` est une balise qui permet de spécifier où se trouve le résumé de la page. Cela permet de ne pas afficher tout le contenu de la page dans la liste des pages.
* Le contenu de la page est écrit en markdown. Un petit guide est donné ci-dessous.

## Markup markdown


Markdown est un langage de balisage léger conçu pour convertir du texte en HTML de manière simple et intuitive. Voici quelques exemples de base pour commencer à utiliser Markdown.

### Titres

Pour créer un titre, utilisez le signe `#` suivi d'un espace, puis de votre titre. Vous pouvez augmenter le nombre de `#` jusqu'à six niveaux de titres.


### Paragraphes

Pour écrire un paragraphe, tapez simplement votre texte. Pour créer un nouveau paragraphe, laissez une ligne vide entre les paragraphes.

### Mise en forme du texte

Vous pouvez mettre en forme votre texte de la manière suivante :

- **Gras** : entourez votre texte avec deux astérisques `**texte**`.
- *Italique* : entourez votre texte avec un astérisque `*texte*`.
- ~~Barré~~ : entourez votre texte avec deux tildes `~~texte~~`.

### Listes

#### Listes non ordonnées

Utilisez des astérisques `*`, des signes plus `+`, ou des tirets `-` pour les éléments de la liste.
* Element 1
* Element 2
  * Sous-element 2.1
  * Sous-element 2.2


#### Listes ordonnées

Pour une liste ordonnée, commencez chaque élément avec un nombre suivi d'un point.

1. Premier élément
2. Deuxième élément
  1. Sous-premier élément
  2. Sous-deuxième élément


### Liens

Pour créer un lien, mettez le texte du lien entre crochets `[]` et l'URL entre parenthèses `()`.

[Ceci est un lien](https://www.exemple.com)


### Citations

Pour créer une citation, utilisez le signe `>` avant le texte.

> Ceci est une citation.


### Code

Pour afficher du code, utilisez des guillemets inversés `` ` `` pour un morceau de code inline, ou trois guillemets inversés ```` ``` ```` pour un bloc de code.

```python
def hello_world():
    print("Hello, world!")
```

### Images

Pour afficher une image, utilisez la syntaxe suivante :
`![Texte alternatif](url_de_l'image)`

### Tableaux

Pour créer un tableau, utilisez des barres verticales `|` pour séparer les colonnes et des tirets `-` pour séparer les en-têtes des autres lignes. Par exemple :
```
| En-tête 1 | En-tête 2 | En-tête 3 |
|-----------|-----------|-----------|
| Cellule 1 | Cellule 2 | Cellule 3 |
| Cellule 4 | Cellule 5 | Cellule 6 |
| Cellule 7 | Cellule 8 | Cellule 9 |
```
créera le tableau suivant :
| En-tête 1 | En-tête 2 | En-tête 3 |
|-----------|-----------|-----------|
| Cellule 1 | Cellule 2 | Cellule 3 |
| Cellule 4 | Cellule 5 | Cellule 6 |
| Cellule 7 | Cellule 8 | Cellule 9 |

### Maths

Par défaut, les maths sont rendus à partir du code LaTex (à condition que le serveur qui héberge la page aie accès à internet car on utilise une librairie sur le web). Ainsi écrire:

```markdown
$$
    \int_{a}^{b} f(x) \, dx
$$
```

donnera:

$$
    \int_{a}^{b} f(x) \, dx
$$


{{<info>}}
Si on a absolument pas besoin de maths, on peut désactiver l'option dans `config.toml` en mettant:
`math = false`.
{{</info>}}

### HTML

Il est possible d'utiliser du code HTML dans les fichiers markdown. Cela peut être utile pour des éléments plus complexes. Pour plus d'informations sur le markdown, consultez [cette page](https://www.markdownguide.org/basic-syntax/).

### Fonctions avancées: shortcode

Un certain nombre de fonctions avancées sont disponibles à l'aide de shortcodes. Pour cela, se référer à la [page dédiée]({{< ref "shortcodes" >}}).

