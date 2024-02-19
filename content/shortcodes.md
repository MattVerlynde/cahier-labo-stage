
---
layout: page
title: Shortcodes
menu:
  main:
    weight: 2
bibFile: content/bibliography.json
mermaid: true
toc: true
---

Description des shortcodes disponibles.

<!--more-->

## Qu'est-ce qu'un shortcode?

En Hugo, un shortcode est un petit morceau de code qui permet d'insérer des éléments dans le contenu d'une page. Il est associé à un code html qui dépends du contenu du shortcode. Par exemple, le shortcode `highlight-block` est associé à un code html qui permet d'insérer un bloc de code coloré dans le contenu de la page.



La syntaxe est toujours la même: `{{</*shortcode*/>}}` avec en fin `{{</*/shortcode*/>}}` pour les shortcodes qui ont un contenu.

S'il y a des paramètres à passer au shortcode, on peut les passer de la manière suivante: `{{</*shortcode "param1" "param2"*/>}}`.

Si le shortcode n'a pas de contenu, on peut utiliser la syntaxe: `{{/%shortcode/%}}`.

## Reference

Pour faire référence à une page du site-web à l'aide de son nom, utilise `{{</*ref nomdelapage*/>}}`. Ainsi par exemple, on peut créer le texte correspondant à la page guide avec `{{</*ref guide*/>}}`, ce qui donne: {{<ref guide>}}. Il ne reste plus qu'à créer le lien comme usuellement en markdown. 


## Highlight-block

Ce shortcode permet d'insérer un bloc de code coloré dans le contenu de la page. Il est associé à un code html qui permet de colorer le code avec la coloration syntaxique du langage de programmation spécifié.

Par exemple:
```markdown
{{</*highlight-block "Définition"*/>}}
Abc
{{</*/highlight-block*/>}}
```

donne:

{{<highlight-block "Définition">}}
Abc
{{</highlight-block>}}


## Proof

Ce shortcode permet de mettre une preuve qui s'affiche lorsque l'on clique sur celle-ci.

Par exemple:
```markdown
{{</*proof*/>}}
Pour montrer le théorème, nous allons d'abord énoncer le lemme suivant:

>Lorem ipsum dolor sit amet, officia excepteur ex fugiat reprehenderit enim labore culpa sint ad nisi Lorem pariatur mollit ex esse exercitation amet. Nisi anim cupidatat excepteur officia. Reprehenderit nostrud nostrud ipsum Lorem est aliquip amet voluptate voluptate dolor minim nulla est proident. Nostrud officia pariatur ut officia. Sit irure elit esse ea nulla sunt ex occaecat reprehenderit commodo officia dolor Lorem duis laboris cupidatat officia voluptate. Culpa proident adipisicing id nulla nisi laboris ex in Lorem sunt duis officia eiusmod. Aliqua reprehenderit commodo ex non excepteur duis sunt velit enim. Voluptate laboris sint cupidatat ullamco ut ea consectetur et est culpa et culpa duis.
{{</*/proof*/>}}
```

donne:
{{<proof>}}
Pour montrer le théorème, nous allons d'abord énoncer le lemme suivant:

>Lorem ipsum dolor sit amet, officia excepteur ex fugiat reprehenderit enim labore culpa sint ad nisi Lorem pariatur mollit ex esse exercitation amet. Nisi anim cupidatat excepteur officia. Reprehenderit nostrud nostrud ipsum Lorem est aliquip amet voluptate voluptate dolor minim nulla est proident. Nostrud officia pariatur ut officia. Sit irure elit esse ea nulla sunt ex occaecat reprehenderit commodo officia dolor Lorem duis laboris cupidatat officia voluptate. Culpa proident adipisicing id nulla nisi laboris ex in Lorem sunt duis officia eiusmod. Aliqua reprehenderit commodo ex non excepteur duis sunt velit enim. Voluptate laboris sint cupidatat ullamco ut ea consectetur et est culpa et culpa duis.
{{</proof>}}


## Info, Warning et Error

Ajouté à highlight block, sont disponibles 3 shortcode: `info`, `warning` et `error` qui permettent d'ajouter des blocs de texte colorés avec une icône correspondant à l'information, un avertissement ou une erreur. L'agument optionnel permet d'ajouter un titre au bloc.

Par exemple:
```markdown
{{</*info Lorem*/>}}
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed et arcu non lectus pellentesque dignissim quis in ligula. Vestibulum consectetur, tellus nec rhoncus tincidunt, lectus nunc bibendum tortor, id pulvinar purus est sed diam. Donec sed purus eu libero volutpat cursus non a nibh. Suspendisse scelerisque vehicula interdum. Sed interdum augue ut justo pulvinar luctus at sit amet libero. Phasellus tincidunt mi sed aliquam gravida. Etiam nec vulputate elit. Nulla nulla purus, scelerisque vitae libero nec, blandit fermentum risus. Donec mi augue, porttitor non varius et, volutpat ut magna. Praesent vulputate urna vel mattis blandit. Integer quis dui et ligula dictum convallis quis sed sem. Morbi pellentesque vestibulum ex et efficitur. Nulla erat turpis, dapibus id est iaculis, faucibus pellentesque sapien. Pellentesque lobortis malesuada elit, ut luctus arcu condimentum ac. Etiam dignissim erat id condimentum efficitur. Nulla id ipsum ultrices, pretium mauris eu, semper elit
{{</*/info*/>}}


{{</*warning*/>}}
Pas de titre
{{</*/warning*/>}}

{{</*error*/>}}
Cette commande supprime tous les fichiers de votre ordinateur:
\`rm -rf /\`
{{</*/error*/>}}
```

qui donne:

{{<info Lorem>}}
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed et arcu non lectus pellentesque dignissim quis in ligula. Vestibulum consectetur, tellus nec rhoncus tincidunt, lectus nunc bibendum tortor, id pulvinar purus est sed diam. Donec sed purus eu libero volutpat cursus non a nibh. Suspendisse scelerisque vehicula interdum. Sed interdum augue ut justo pulvinar luctus at sit amet libero. Phasellus tincidunt mi sed aliquam gravida. Etiam nec vulputate elit. Nulla nulla purus, scelerisque vitae libero nec, blandit fermentum risus. Donec mi augue, porttitor non varius et, volutpat ut magna. Praesent vulputate urna vel mattis blandit. Integer quis dui et ligula dictum convallis quis sed sem. Morbi pellentesque vestibulum ex et efficitur. Nulla erat turpis, dapibus id est iaculis, faucibus pellentesque sapien. Pellentesque lobortis malesuada elit, ut luctus arcu condimentum ac. Etiam dignissim erat id condimentum efficitur. Nulla id ipsum ultrices, pretium mauris eu, semper elit
{{</info>}}


{{<warning>}}
Pas de titre
{{</warning>}}

{{<error>}}
La commande suivante supprime tous les fichiers de votre ordinateur:
`rm -rf /`
{{</error>}}




## Cite

Ce shortcode permet d'insérer une citation dans le contenu de la page. Avant de l'utiliser il faut définir un fichier de citations. Pour plus de détails, voir [ici](https://labs.loupbrun.ca/hugo-cite/usage/#render-in-text-citations).

Un exemple:
```markdown
blabla comme montré dans {{</*cite "gordana_thesis"*/>}}
```

donne:

blabla comme montré dans {{<cite "gordana_thesis">}}

## Bibliography

Ce shortcode permet d'insérer une bibliographie dans le contenu de la page. Avant de l'utiliser il faut définir un fichier de bibliographie. Pour plus de détails, voir [ici](https://labs.loupbrun.ca/hugo-cite/usage/#display-a-bibliography).

Un exemple:
```markdown
{{</*bibliography*/>}}
```

donne:
{{<bibliography>}}

alors que:
```markdown
{{</*bibliography cited*/>}}
```

donne:
{{<bibliography cited>}}


## Mermaid

Il est posible d'insérer des diagrammes avec le langage Mermaid. Pour plus de détails, voir [ici](https://mermaid-js.github.io/mermaid/#/). Il faut cela dit, insérer `mermaid: true` dans le préambule de la page. Comme pour les maths, il faut un accès internet pour que cela fonctionne. La syntaxe est la suivante:

```markdown
{{</*mermaid*/>}}
Le contenu
{{</*/mermaid*/>}}
```

On peut ainsi faire des choses comme:


{{< mermaid >}}
flowchart TB
    a1-->c1
    subgraph A
    a1-->a2
    end
    subgraph B
    b1-->b2
    end
    subgraph C
    c1-->c2
    end
{{< /mermaid >}}

## Attachments

Si l'on veut attacher des fichiers à la page, on doit créer un dossier `nompage.files` et utiliser:

```markdown
{{%/*attachments /*/%}}
```

ce qui sur cette page donne:

{{%attachments /%}}

## Chart

Pour insérer des graphes utiliser le shortcode `chart`, qui est basé sur chart-js (voir [ici](https://www.chartjs.org/) dans la verison `3.9.1`). Comme pour les maths on aura besoin d'un accès internet. Un exemple d'utilisation:

```markdown
{{</* chart 75 400 true*/>}}
{
  type: 'line',
  data: {
    labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
    datasets: [{
      label: 'My First Dataset',
      data: [65, 59, 80, 81, 56, 55, 40],
      fill: false,
      borderColor: 'rgb(75, 192, 192)',
      tension: 0.1,
    },
    {
      label: 'My Second Dataset',
      data: [35, 49, 70, 71, 46, 45, 30],
      fill: true,
      borderColor: 'rgb(192, 75, 192)',
      tension: 0.1,
    }]
  },
  options: {
    maintainAspectRatio: false,
    plugins:{
      title: {
        display: true,
        text: 'Série temporelle',
        font: {
          size: 18
        }
      }
    }
  }
}
{{</* /chart */>}}

```

Les deux arguments sont la largeur (en % de la page) et la hauteur en pixels. Un dernier argument permet d'activer une option de zoom désactivée par défaut. Cet example donne:


{{< chart 75 400 true>}}
{
  type: 'line',
  data: {
    labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
    datasets: [{
      label: 'My First Dataset',
      data: [65, 59, 80, 81, 56, 55, 40],
      fill: false,
      borderColor: 'rgb(75, 192, 192)',
      tension: 0.1,
    },
    {
      label: 'My Second Dataset',
      data: [35, 49, 70, 71, 46, 45, 30],
      fill: true,
      borderColor: 'rgb(192, 75, 192)',
      tension: 0.1,
    }]
  },
  options: {
    maintainAspectRatio: false,
    plugins:{
      title: {
        display: true,
        text: 'Série temporelle',
        font: {
          size: 18
        }
      }
    }
  }
}
{{< /chart >}}

{{<warning>}}
La propriété `maintainAspectRatio` doit être `false` pour que le graphique s'affiche correctement avec la taille souhaitée.
{{</warning>}}

## Pseudo-code

Il est possible de mettre du pseudo-code dans les pages. Pour cela, on utilise le shortcode `pseudocode`. On a également besoin d'internet (dû à MathJax, ne pas désactiver l'option `math:true` dans le fichier `config.toml` si on veut utiliser cela). Par exemple:

```markdown
{{</*pseudocode*/>}}
\begin{algorithm}
\caption{Quicksort}
\begin{algorithmic}
\PROCEDURE{Quicksort}{$A, p, r$}
    \IF{$p < r$} 
        \STATE $q = $ \CALL{Partition}{$A, p, r$}
        \STATE \CALL{Quicksort}{$A, p, q - 1$}
        \STATE \CALL{Quicksort}{$A, q + 1, r$}
    \ENDIF
\ENDPROCEDURE
\PROCEDURE{Partition}{$A, p, r$}
    \STATE $x = A[r]$
    \STATE $i = p - 1$
    \FOR{$j = p$ \TO $r - 1$}
        \IF{$A[j] < x$}
            \STATE $i = i + 1$
            \STATE exchange
            $A[i]$ with     $A[j]$
        \ENDIF
        \STATE exchange $A[i]$ with $A[r]$
    \ENDFOR
\ENDPROCEDURE
\end{algorithmic}
\end{algorithm}
{{</*/pseudocode*/>}}
```

donne:

{{<pseudocode>}}
\begin{algorithm}
\caption{Quicksort}
\begin{algorithmic}
\PROCEDURE{Quicksort}{$A, p, r$}
    \IF{$p < r$} 
        \STATE $q = $ \CALL{Partition}{$A, p, r$}
        \STATE \CALL{Quicksort}{$A, p, q - 1$}
        \STATE \CALL{Quicksort}{$A, q + 1, r$}
    \ENDIF
\ENDPROCEDURE
\PROCEDURE{Partition}{$A, p, r$}
    \STATE $x = A[r]$
    \STATE $i = p - 1$
    \FOR{$j = p$ \TO $r - 1$}
        \IF{$A[j] < x$}
            \STATE $i = i + 1$
            \STATE exchange
            $A[i]$ with     $A[j]$
        \ENDIF
        \STATE exchange $A[i]$ with $A[r]$
    \ENDFOR
\ENDPROCEDURE
\end{algorithmic}
\end{algorithm}
{{</pseudocode>}}

{{<info>}}
Il faut parfois relancer la page pour que l'affichage fasse effet. (Importation de MathJax).
{{</info>}}
