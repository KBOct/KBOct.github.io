---
title: "Numerical Analysis for Ordinary Differential Equations : Red Blood Cell agglomeration (French) - Part 2"
date: 2019-01-17
categories: [numerical analysis, scilab]
tags: [numerical analysis, ode, scilab]
header:
  image: "/images/1- numerical analysis for ODEs/RBC banner2.jpg"
excerpt: "This is the second part of my 3-part series on using Numerical Analysis of ODEs to model RBC agglomeration Here"
mathjax: true
author_profile: false
toc : true
---

# Partie 2 : Dynamique des populations en compétition

Le but de cette partie est d’étudier un modèle où deux espèces sont en compétition. Dans la suite $$x_1$$ et $$x_2$$ correspondent à la population des deux espèces. On considère le modèle

### Question a

Quand $$b_1=b_2=0$$ :
$$(2)\Leftrightarrow \left\{ \begin{array}{c}
{x^'}_1\left(t\right)=a_1x_1\left(t\right)(1-x_1\left(t\right)) \\
{x^'}_2\left(t\right)=a_2x_2\left(t\right)(1-x_2\left(t\right)) \\
{(x}_1\left(0\right),x_1\left(0\right))=(x^{ini}_1,x^{ini}_2) \end{array}
\right.$$

et on retrouve le modèle logistique de la première partie. Les espèces n'ont aucune influence mutuelle.

### Question b

Une fonction $$G : \mathbb{R} \times  \mathbb{R}^2 \rightarrow \mathbb{R}^2$$ telle que $$({x_1}^′(t), {x_2}^′(t)) = G(t, (x_1(t), x_2(t)))$$
est donnée par :

$$G(t,\left(\genfrac{}{}{0pt}{}{x_1(t)}{x_2(t)}\right))=\left(\genfrac{}{}{0pt}{}{a_1x_1(t)(1-{x_1\left(t\right)-b}_1x_2(t))}{a_2x_2(t)(1-{x_2\left(t\right)-b}_2x_1(t))}\right)$$

### Question c

Pour les premiers paramètres, on obtient le champ de vecteurs associé à G suivant :

IMAGE CHP VECT 1

Pour le deuxième jeu de paramètres, on a :

IMAGE CHP VECT 2

### Question d

Dans le premier cas, le point attracteur est (0;1). Dans le deuxième cas, le point attracteur est (3/8;3/4). Le premier cas représente l'extinction de l'espèce $$x_1$$ alors que le deuxième constitue une sorte d'équilibre entre les deux espèces.

### Question e

Vérification numérique à travers le schéma de Runge-Kutta pour le premier jeu de paramètres :

2 IMAGES

Les coordonnées du premier attracteur correspond aux asymptotes de chacune des courbes représentant les évolutions respectives de $$x_1$$ et $$x_2$$ .

2 AUTRES IMAGES

Pour le deuxième jeu, on remarque la même chose, une asymptote en $$x_1\ \ =\ 3/8\ et\ x_2\ =\ 3/4$$ qui correspondent aux coordonnées du deuxième attracteur :


{% highlight matlab linenos %}
code
{% endhighlight %}


![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/1.jpg)
{:class="img-responsive"}
<!-- {: .full} -->
