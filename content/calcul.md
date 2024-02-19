
---
layout: page
title: Quelques notes de calculs
menu:
  main:
    weight: 5
toc: False
---

Exemple de page détaillant des calculs mathématiques.

<!--more-->

A titre d'exmple redérivons l'estimateur au sens de maximum de vraissemblance des paramètres d'une loi Gaussienne multivariée: 

{{<highlight-block "Proposition" >}}
  Soit un ensemble de données $\{\mathbf{x}_k:1\leq k \leq n\}$ iid suivant une loi Gaussienne multivariée $\mathcal{N}(\boldsymbol{\mu},\boldsymbol{\Sigma})$. L'estimateur au sens de maximum de vraissemblance des paramètres $\boldsymbol{\mu}$ et $\boldsymbol{\Sigma}$ est donné par:

$$
\begin{aligned}
    \hat{\boldsymbol{\mu}}&=\frac{1}{n}\sum_{k=1}^{n}\mathbf{x}\_k\\
    \hat{\boldsymbol{\Sigma}}&=\frac{1}{n}\sum_{k=1}^{n}(\mathbf{x}_k-\hat{\boldsymbol{\mu}})(\mathbf{x}_k-\hat{\boldsymbol{\mu}})^T
\end{aligned}
$$
{{</highlight-block>}}

{{<proof>}}
Commençons par écrire la densité de probabilité de la loi Gaussienne multivariée:
$$
    f(\mathbf{x};\boldsymbol{\mu},\boldsymbol{\Sigma})=\frac{1}{(2\pi)^{n/2}|\boldsymbol{\Sigma}|^{1/2}}\exp\left(-\frac{1}{2}(\mathbf{x}-\boldsymbol{\mu})^T\boldsymbol{\Sigma}^{-1}(\mathbf{x}-\boldsymbol{\mu})\right).
$$
La log-vraissemblance est donnée par:
$$
    \log\mathcal{L}(\mathbf{x}\_1,\ldots,\mathbf{x}\_n;\boldsymbol{\mu},\boldsymbol{\Sigma})=\sum_{k=1}^{n}\log f(\mathbf{x}_k;\boldsymbol{\mu},\boldsymbol{\Sigma})
$$

Dériver cette log-vraissemblance par rapport à $\boldsymbol{\mu}$ et $\boldsymbol{\Sigma}$ donne les équations suivantes:
$$
    \begin{aligned}
        \frac{\partial \log\mathcal{L}}{\partial \boldsymbol{\mu}}&=\sum_{k=1}^{n}\boldsymbol{\Sigma}^{-1}(\mathbf{x}\_k-\boldsymbol{\mu})\\
        \frac{\partial \log\mathcal{L}}{\partial \boldsymbol{\Sigma}}&=\frac{1}{2}\sum_{k=1}^{n}\left(\boldsymbol{\Sigma}^{-1}-(\mathbf{x}\_k-\boldsymbol{\mu})(\mathbf{x}_k-\boldsymbol{\mu})^T\right)
    \end{aligned}
$$

En solvant ces équations pour $\boldsymbol{\mu}$ et $\boldsymbol{\Sigma}$ on obtient les estimateurs proposés.
{{</proof>}}

{{<highlight-block "Remarque">}}
  Ces estimateurs sont biaisés, mais asymptotiquement non biaisés. 

{{<proof>}}
  Pour montrer que ces estimateurs sont asymptotiquement non biaisés, on peut utiliser le théorème central limite pour montrer que:
  $$
  \begin{aligned}
      \sqrt{n}(\hat{\boldsymbol{\mu}}-\boldsymbol{\mu})&\overset{d}{\longrightarrow}\mathcal{N}(\mathbf{0},\boldsymbol{\Sigma})\\
      \sqrt{n}(\hat{\boldsymbol{\Sigma}}-\boldsymbol{\Sigma})&\overset{d}{\longrightarrow}\mathcal{N}(\mathbf{0},\boldsymbol{\Sigma}^2)
  \end{aligned}
  $$
  où $\overset{d}{\longrightarrow}$ signifie convergence en loi. Ainsi, on peut montrer que:
  $$
  \begin{aligned}
      \mathbb{E}[\hat{\boldsymbol{\mu}}]&=\boldsymbol{\mu}+\frac{1}{\sqrt{n}}\mathbb{E}[\mathcal{N}(\mathbf{0},\boldsymbol{\Sigma})]=\boldsymbol{\mu}\\
      \mathbb{E}[\hat{\boldsymbol{\Sigma}}]&=\boldsymbol{\Sigma}+\frac{1}{\sqrt{n}}\mathbb{E}[\mathcal{N}(\mathbf{0},\boldsymbol{\Sigma}^2)]=\boldsymbol{\Sigma}
  \end{aligned}
  $$
  On peut montrer que ces estimateurs sont asymptotiquement non biaisés.
{{</proof>}}


{{</highlight-block>}}

On peut ainsi comparer l'estimation à la borne de Cramer-Rao pour évaluer la qualité de l'estimateur et montrer qu'ils sont efficaces.

{{<highlight-block "Définition">}}

Dans le cas multivarié, soit un modèle statistique paramétré par un vecteur de paramètres $\boldsymbol{\theta}$ associé à une loi de probabilité $f(\mathbf{x};\boldsymbol{\theta})$. Soit un estimateur $\hat{\theta}$ donné, on définit la borne de Cramer-Rao à l'aide de l'inégalité suivante:
$$
    \mathrm{Var}(\hat{\boldsymbol{\theta}})\geq \mathcal{I}^{-1}(\boldsymbol{\theta}),
$$

où $\mathcal{I}(\boldsymbol{\theta})$ est la matrice d'information de Fisher définie par
:
$$
    \mathcal{I}(\boldsymbol{\theta})=\mathbb{E}\left[\frac{\partial \log f(\mathbf{x};\boldsymbol{\theta})}{\partial \boldsymbol{\theta}}\frac{\partial \log f(\mathbf{x};\boldsymbol{\theta})}{\partial \boldsymbol{\theta}^T}\right].
$$

{{</highlight-block>}}

L'efficacité correspond alors au cas: $\mathrm{Var}(\hat{\boldsymbol{\theta}})=\mathcal{I}^{-1}(\boldsymbol{\theta})$. 
{{<highlight-block "Proposition">}}
  Les estimateurs proposés sont efficaces en montrant que la borne de Cramer-Rao est atteinte.
{{</highlight-block>}}

{{<proof>}}
Il suffit de montrer que la borne de Cramer-Rao est atteinte. Pour cela, on peut montrer que la matrice d'information de Fisher est donnée par:
$$
    \begin{aligned}
        \mathcal{I}(\boldsymbol{\mu})&=n\boldsymbol{\Sigma}^{-1}\\
        \mathcal{I}(\boldsymbol{\Sigma})&=\frac{n}{2}\boldsymbol{\Sigma}^{-1}\otimes\boldsymbol{\Sigma}^{-1}
    \end{aligned}
$$
En calculant la variance des estimateurs on tombe sur le résultat.
{{</proof>}}



