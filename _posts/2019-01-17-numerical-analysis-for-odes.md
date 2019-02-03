---
title: "Numerical Analysis for Ordinary Differential Equations : Red Blood Cell agglomeration (French)"
date: 2019-01-17
categories: [numerical analysis, scilab]
tags: [numerical analysis, ode, scilab]
header:
  image: "/images/1- numerical analysis for ODEs/RBC banner2.jpg"
excerpt: "Here, I model Red Blood Cell (Haematocyte) agglomeration using scilab. This was my Bachelor's thesis project for which I got a 18/20 (Grade A)"
mathjax: true
author_profile: false
---

# Partie 1 : dynamique des populations

## Cas d’une seule population : l’équation logistique

### Question a

On souhaite résoudre l'equation  : $$\left\{ \begin{array}{rcl}
x'\left(t\right)=ax\left(t\right)(1-x\left(t\right)) \\
x(0)=x^{ini} \end{array}
\right.$$

Cherchons d'abord les solutions constantes. Si $$x(t)\ =\ C\mathrm{\ }\in \mathrm{\ }\mathbb{R}$$ pour tout $$t\mathrm{\ }\in \mathrm{\ }\mathbb{R}$$ alors $$x'(t)\ =\ 0$$ pour tout $$t\mathrm{\ }\in \mathrm{\ }\mathbb{R}$$ et l'EDO devient $$0\ =\ aC(1-C)$$.

Par conséquent $$C\ =\ 0\ ou\ C=1$$. Mais, la fonction constante $$x_1(t)\ =\ 0\ $$ne vérifie pas  $$x_1(0)\ =\ x^{ini}>0$$ donc $$x_2(t)\ =\ 1$$ est la seule solution sur $$\mathbb{R}$$ de l'EDO avec la condition initiale $$x(0)\ =\ x^{ini}>0$$.


Soit $$x$$ une solution (non constante) sur un intervalle $$I$$ de $$\mathbb{R}$$ contenant 0 et sur lequel $$x(t)\ \in \ \left]0,1\right[$$  pour tout $$t\ \in \ I$$. Alors on peut écrire :
$$f(t,x(t))=\frac{d}{dt}x\left(t\right)=ax\left(t\right)\left(1-x\left(t\right)\right)$$

où $$f$$ est la fonction second membre de l'EDO qui peut être écrite~:


$$f(t,x)=\frac{dx}{dt}=ax(1-x)=g(t)h(x)$$


 en posant $$g\left(t\right)=a$$ et $$\ h\left(x\right)=x\left(1-x\right)$$. $$f$$ est donc à variables séparées.

### Question b

Définition de la fonction de population solution non constante de l'EDO (1), obtenue par résolution analytique :

{% highlight matlab linenos %}
function x=population(t,xini,a)
x=xini./(xini+(1-xini)* exp(-a*t));
endfunction

//f : fonction second membre associée à l'EDO (1)
function [y]= f(t,x)
   y=a*x*(1-x);
endfunction

{% end highlight %}
TEXTE
{% highlight matlab linenos %}
lol
{% end highlight %}
