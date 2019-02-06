---
title: "Numerical Analysis for Ordinary Differential Equations : Red Blood Cell agglomeration (French) - Part 3"
date: 2019-01-17
categories: [numerical analysis, scilab]
tags: [numerical analysis, ode, scilab]
header:
  image: "/images/1- numerical analysis for ODEs/RBC banner2.jpg"
excerpt: "Third and last part of the series, wherein the actual numerical scheme to model RBC agglomeration is introduced and implemented"
mathjax: true
author_profile: false
toc : true
---

# Partie 3 : Modélisation des globules rouges

NB : **Cette partie est indépendante des précédentes.** On s’intéresse à la modélisation du phénomène d’agglomération des globules rouges. Ces derniers vont être modélisés par des particules ponctuelles
interagissant par une force d’interaction liée à la distance entre deux globules rouges voisins.

## Description du modèle

On considère $$M+2$$ globules rouges (aussi appelés hématies), représentés par des particules ponctuelles placées sur l’intervalle $$[0, 1]$$. Chaque globule rouge subit l’influence de ses deux voisins, sauf les deux globules des extrémités, qui sont placés respectivement en $$0$$ et en $$1$$. On note $$x_i(t)$$ la position de l’hématie $$i$$ au temps $$t$$. L’indice varie entre $$0$$ et $$M + 1$$.

On a donc :

$$0 = x_0(t) < x_1(t) < ... < x_M(t) < x_{M+1}(t) = 1$$

La force d’interaction entre les particules est attractive lorsque la distance entre deux particules est assez grande, et décroit vers $$0$$ lorsque cette distance tend vers l’infini. \\ De plus, pour prendre en compte la taille des globules, on considère que la force d’interaction devient répulsive lorsque la distance entre les hématies devient inférieure à une valeur $$r$$, correspondant à la taille caractéristique (typiquement le rayon) de l’hématie.\\ Cette force d’interaction va être modélisée par une fonction I de la distance entre les particules qui va donc tendre vers $$0$$ à l’infini, qui sera positive pour une distance $$d > r$$ et négative pour une distance $$d < r$$.

On choisit ici la fonction suivante :

$$ I : \begin{array}{rcl} \mathbb{R}^+ \rightarrow \mathbb{R} \\
d \mapsto \frac{c}{d} \ln \ \left\( \frac{d}{r}\right)  \end{array}
\right.$$


où $$c$$ est un paramètre qui caractérise l’intensité de la force.
Les forces exercées sur l’hématie i comportent
donc la force exercée par l’hématie $$i + 1$$, qui est $$I(x_{i+1} - x_i)$$, la force exercée par l’hématie $$i − 1$$, qui est $$−I(x_i − x_{i−1})$$, ainsi qu’une force de frottement qui est proportionnelle à la vitesse de l’hématie, et s’oppose au mouvement de la particule : $$−\alpha \dot{x_i}$$, où $$\alpha$$ est appelé coefficient de frottement.

Les équations du mouvement associées à ce système s’écrivent donc :

$$ \forall \i \ = 1,\dots, M,  \left\{ \begin{array}{l}
m \ddot{x_i} = I(x_{i+1} - x_i) - I(x_i - x_{i-1}) -\alpha \dot{x_i}\ \\
x_i \left(0\right)=x^i_{ini} \\
x^\prime_i \left(0\right)=v^i_{ini} \end{array}
\right.$$

où $$m$$ est la masse d’un globule rouge (prise égale à $$1$$ dans la suite).



## Schéma numérique de base

### Equations du schéma numérique pour M=3

On souhaite simuler numériquement le modèle précédent. On commence par définir T le temps final
de la simulation. On discrétise l’intervalle [0, T] en K intervalles égaux [tk, tk+1] pour k allant de 0 à
K − 1 avec tk = kh, et h = T
K.
On s’intéresse au schéma suivant (k désigne un entier positif ou nul)


x0
i = xini
i ,
x1
i = x0
i + tvini
i ,
wk
i = I(xk
i+1 − xk
i ) − I(xk
i − xk
i−1),
xk+1
i = 2xk
i − xk−1
i + t2wk
i −t(xk
i − xk−1
i ).
On utilisera les paramètres suivants : K = 1500, t = 0.002,  = 10, c = 0.5, r = 0.004.


![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/1.jpg)
{:class="img-responsive"}
<!-- {: .full} -->


{% highlight matlab linenos %}

{% endhighlight %}
