---
title: "Numerical Analysis for Ordinary Differential Equations : Red Blood Cell agglomeration (French) - Part 2"
date: 2019-01-17
categories: [numerical analysis, scilab]
tags: [numerical analysis, ode, scilab]
header:
  image: "/images/1- numerical analysis for ODEs/RBC banner2.jpg"
excerpt: "This is the second part of my 3-part series on using Numerical Analysis of ODEs to model RBC agglomeration"
mathjax: true
author_profile: false
toc : true
---

# Partie 2 : Dynamique des populations en compétition

Le but de cette partie est d’étudier un modèle où deux espèces sont en compétition. Dans la suite $$x_1$$ et $$x_2$$ correspondent à la population des deux espèces. On considère le modèle :\\

$$ (3) \left\{ \begin{array}{c}
x^\prime_1 \left(t\right)=a_1x_1\left(t\right)(1-x_1\left(t\right)-b_2x_1\left(t\right)) \\
x^\prime_2 \left(t\right)=a_2x_2\left(t\right)(1-x_2\left(t\right)-b_1x_2\left(t\right)) \\
\left(x_1(0),x_2(0)\right)=(x^{ini}_1,x^{ini}_2) \end{array}
\right.$$

### Question a

Quand $$b_1=b_2=0$$ :

$$ (3) \Leftrightarrow  \left\{ \begin{array}{c}
x^\prime_1 \left(t\right)=a_1x_1\left(t\right)(1-x_1\left(t\right)) \\
x^\prime_2 \left(t\right)=a_2x_2\left(t\right)(1-x_2\left(t\right)) \\
\left(x_1(0),x_2(0)\right)=(x^{ini}_1,x^{ini}_2) \end{array}
\right.$$


<!-- \label{"1"}$$ -->

et on retrouve le modèle logistique de la première partie. Les espèces n'ont aucune influence mutuelle.

### Question b

Une fonction $$G : \mathbb{R} \times  \mathbb{R}^2 \rightarrow \mathbb{R}^2$$ telle que $$({x_1}^′(t), {x_2}^′(t)) = G(t, (x_1(t), x_2(t)))$$
est donnée par :

$$G\left(t,\left(\genfrac{}{}{0pt}{}{x_1(t)}{x_2(t)}\right)\right)=\left(\genfrac{}{}{0pt}{}{a_1x_1(t)(1-{x_1\left(t\right)-b}_1x_2(t))}{a_2x_2(t)(1-{x_2\left(t\right)-b}_2x_1(t))}\right)$$

### Question c

{% highlight matlab linenos %}

function y=G(t,x)
    y(1)=a1*x(1) * (1-x(1)-b1*x(2))
    y(2)=a2*x(2) * (1-x(2)-b2*x(1))
endfunction

[a1,b1,a2,b2]=(1.66,1.1316,1.66,0.8688);

scf(1)
clf
fchamp(G, 0., 0:(1/50):1, 0:(1/50):1)
title("Champ de vecteurs associé au premier jeu de paramètres",'fontsize',2)

{% endhighlight %}

Pour les premiers paramètres, on obtient le champ de vecteurs associé à G suivant :

![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/2-1.jpg)
{:class="img-responsive"}

{% highlight matlab linenos %}

[a1,b1,a2,b2]=(0.5,0.8,1,0.6);

scf(2)
clf
fchamp(G, 0., 0:(1/50):1, 0:(1/50):1)
title("Champ de vecteurs associé au deuxième jeu de paramètres",'fontsize',2)

{% endhighlight %}

Pour le deuxième jeu de paramètres, on a :

![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/2-2.jpg)
{:class="img-responsive"}

### Question d

Dans le premier cas, le point attracteur est $$(0;1)$$. Dans le deuxième cas, le point attracteur est $$\left(\frac{3}{8};\frac{3}{8} \right)$$. Le premier cas représente l'extinction de l'espèce $$x_1$$ alors que le deuxième constitue une sorte d'équilibre entre les deux espèces.

### Question e

Vérification numérique à travers le schéma de Runge-Kutta pour le premier jeu de paramètres :

{% highlight matlab linenos %}

T=100;
deltat = 0.1;

//Schéma de Runge Kutta 4
function X= RK4(t,deltat,f,x0)
    X(:,1)=x0
    N=length(t)
    for n=1:(N-1)
        Xk1 = X(:,n)
        k1 = f(t,Xk1)
        Xk2 = Xk1 + 0.5*deltat*k1
        k2 = f(t,Xk2)
        Xk3 = Xk1 + 0.5*deltat*k2
        k3 = f(t,Xk3)
        Xk4 = Xk1 + deltat*k3
        k4 = f(t,Xk4)       
        X(:,n+1) = X(:,n) + deltat/6 * (k1 + 2*k2 + 2*k3 + k4)
    end
endfunction

//Premier jeu de paramètres
[a1,b1,a2,b2]=(1.66,1.1316,1.66,0.8688);

t=linspace(0,T,(T/deltat));

Xpremierjeu1 = RK4(t,deltat,G,[0.3;0.1])
Xpremierjeu2 = RK4(t,deltat,G,[0.02;0.35])
Xpremierjeu3 = RK4(t,deltat,G,[0.9;0.8])

scf(3)
clf
subplot(2,1,1)
plot(t,Xpremierjeu1(1,:),'r')
plot(t,Xpremierjeu2(1,:),'b--')
plot(t,Xpremierjeu3(1,:),'k.')
title("Evolution de x1 en fonction du temps pour le premier jeu de paramètres",'fontsize',2)

ylabel('x1')
xlabel('t')

subplot(2,1,2)
plot(t,Xpremierjeu1(2,:),'r')
plot(t,Xpremierjeu2(2,:),'b--')
plot(t,Xpremierjeu3(2,:),'k.')

ylabel('x2')
xlabel('t')
title("Evolution de x2 en fonction du temps pour le premier jeu de paramètres",'fontsize',2)

{% endhighlight %}

![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/2-3.jpg)
{:class="img-responsive"}

Les coordonnées du premier attracteur correspond aux asymptotes de chacune des courbes représentant les évolutions respectives de $$x_1$$ et $$x_2$$ .

{% highlight matlab linenos %}

//Deuxième jeu de paramètres
[a1,b1,a2,b2]=(0.5,0.8,1,0.6);


Xdeuxiemejeu1 = RK4(t,deltat,G,[0.3;0.1])
Xdeuxiemejeu2 = RK4(t,deltat,G,[0.02;0.35])
Xdeuxiemejeu3 = RK4(t,deltat,G,[0.9;0.8])

scf(4)
clf
subplot(2,1,1)
plot(t,Xdeuxiemejeu1(1,:),'r')
plot(t,Xdeuxiemejeu2(1,:),'b--')
plot(t,Xdeuxiemejeu3(1,:),'k.')

ylabel('x1')
xlabel('t')
title("Evolution de x1 en fonction du temps pour le deuxième jeu de paramètres",'fontsize',2)

subplot(2,1,2)
plot(t,Xdeuxiemejeu1(2,:),'r')
plot(t,Xdeuxiemejeu2(2,:),'b--')
plot(t,Xdeuxiemejeu3(2,:),'k.')

ylabel('x2')
xlabel('t')
title("Evolution de x2 en fonction du temps pour le deuxième jeu de paramètres",'fontsize',2)

{% endhighlight %}

![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/2-4.jpg)
{:class="img-responsive"}

Pour le deuxième jeu, on remarque la même chose, une asymptote en $$x_1\ \ =\ 3/8\ $$ et $$\ x_2\ =\ 3/4 $$ qui correspondent aux coordonnées du deuxième attracteur :
