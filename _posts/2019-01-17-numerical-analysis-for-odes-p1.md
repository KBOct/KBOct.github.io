---
title: "Numerical Analysis for Ordinary Differential Equations : Red Blood Cell agglomeration (French) - Part 1"
date: 2019-01-17
categories: [numerical analysis, scilab]
tags: [numerical analysis, ode, scilab]
header:
  image: "/images/1- numerical analysis for ODEs/RBC banner2.jpg"
excerpt: "Here, I model Red Blood Cell (Haematocyte) agglomeration using scilab. This was my Bachelor's thesis project for which I got a 18/20 (Grade A)"
author_profile: false
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---

# Partie 1 : Dynamique des populations

Dans cette première partie, on s’intéresse à des modèles de dynamique des populations, qui servent à
décrire l’évolution conjointe de plusieurs espèces ou populations (d’animaux ou de cellules par exemple).

## Cas d’une seule population : l’équation logistique

Le modèle logistique consiste à garder un comportement de croissance exponentielle lorsqu’il y a peu
d’individus, avec limitation lorsque le nombre d’individus s’approche d’une valeur prescrite (il y alors trop d’individus pour la quantité limitée d’espace et/ou de ressources à disposition). Dans la suite, on normalise cette valeur prescrite à 1.


Ce modèle s’écrit : $$\left\{ \begin{array}{l}
x'\left(t\right)=ax\left(t\right)(1-x\left(t\right)) \\
x(0)=x^{ini} \end{array}
\right.\label{eq:1}$$

### Résolution analytique de l’équation par la méthode de séparation des variables

Cherchons d'abord les solutions constantes. Si $$x(t)\ =\ C\mathrm{\ }\in \mathrm{\ }\mathbb{R}$$ pour tout $$t\mathrm{\ }\in \mathrm{\ }\mathbb{R}$$ alors $$x'(t)\ =\ 0$$ pour tout $$t\mathrm{\ }\in \mathrm{\ }\mathbb{R}$$ et l'EDO devient $$0\ =\ aC(1-C)$$.

Par conséquent $$C\ =\ 0\ ou\ C=1$$. Mais, la fonction constante $$x_1(t)\ =\ 0\ $$ne vérifie pas  $$x_1(0)\ =\ x^{ini}>0$$ donc $$x_2(t)\ =\ 1$$ est la seule solution sur $$\mathbb{R}$$ de l'EDO avec la condition initiale $$x(0)\ =\ x^{ini}>0$$.

Soit $$x$$ une solution (non constante) sur un intervalle $$I$$ de $$\mathbb{R}$$ contenant 0 et sur lequel $$x(t)\ \in \ \left]0,1\right[$$  pour tout $$t\ \in \ I$$. Alors on peut écrire :
$$f(t,x(t))=\frac{d}{dt}x\left(t\right)=ax\left(t\right)\left(1-x\left(t\right)\right)$$
où $$f$$ est la fonction second membre de l'EDO qui peut être écrite :
$$f(t,x)=\frac{dx}{dt}=ax(1-x)=g(t)h(x)$$
en posant $$g\left(t\right)=a$$ et $$\ h\left(x\right)=x\left(1-x\right)$$. $$f$$ est donc à variables séparées.

Puisque $$x(t)\neq 0\ et\ x(t)\neq 1\ $$on peut diviser les deux membres de l'équation par $$x\left(1-x\right)$$, on obtient :

$$\frac{dx}{x(1-x)}=a\ dt$$

En intégrant les deux membres, on obtient :

$$\int{\frac{dx}{x(1-x)}}=\int{a\ dt}$$

or par décomposition en éléments simples on obtient :

$$\int{(\frac{1}{x}}+\frac{1}{1-x})\ dx=\int{a\ dt}$$

puis :
$$ln(\frac{x}{x^{ini}})-ln(\frac{1-x}{1-x^{ini}})=at$$

Donc, pour $$t\ \in \ I$$ :
$$x(t)=\frac{x^{ini}}{x^{ini}+({1-x}^{ini})e^{-at}}$$

### Solution de l'EDO

Définition de la fonction de population solution non constante de l'EDO (1), obtenue précédemment par résolution analytique :

{% highlight matlab linenos %}
function x=population(t,xini,a)
x=xini./(xini+(1-xini)* exp(-a*t));
endfunction

//f : fonction second membre associée à l'EDO (1)
function [y]= f(t,x)
   y=a*x*(1-x);
endfunction

//Schéma d'Euler modifié dit méthode du point milieu
function [t,x]=EulerModifie(f,tini,xini,h,N)
if size(xini,1) < size(xini,2) then
   xini=xini'
end
   t=zeros(1,N+1)
   x=zeros(length(xini),N+1)
   t(1)=tini
   x(:,1)=xini
   for j = 1:N
       t(j+1)=t(j)+h
       x(:,j+1)=x(:,j)+h*f(t(j)+(h/2),x(:,j)+(h/2)* f(t(j),x(:,j)))
   end
endfunction

{% endhighlight %}

Puis on définit les solutions constantes :

{% highlight matlab linenos %}

//paramètres
a = 1.5;
T = 5;
deltat = 0.1;
N=T/deltat;
t = linspace(0,N,2*(N+1))

//solutions constantes de l'EDO (1)
function y =xconstante1(t)
   y=1;
endfunction

function y =xconstante2(t)
   y=0;
endfunction
{% endhighlight %}

Solutions de l'EDO (1) et leurs approximations :

![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/1.jpg)
{:class="img-responsive"}
<!-- {: .full} -->

Les solutions tendent toutes vers la solution constante de valeur 1 et l'approximation par la méthode du point milieu de chacune des solutions est très bonne avec les paramètres utilisés .

### Ordre de la méthode

Déterminons l'ordre de la méthode du point milieu en traçant l'erreur de consistance en échelle logarithmique :

{% highlight matlab linenos %}

xtn1=zeros(1,N+1);
xtn2=zeros(1,N+1);
xtn3=zeros(1,N+1);
xtn4=zeros(1,N+1);

cpt=1;

for deltat=[0.1, 0.01, 0.001]
  N=T/deltat;
  [tps1,xa1]= EulerModifie(f,0,0.1,deltat,N);
  [tps2,xa2]= EulerModifie(f,0,0.5,deltat,N);
  [tps3,xa3]= EulerModifie(f,0,1.1,deltat,N);
  [tps4,xa4]= EulerModifie(f,0,1.5,deltat,N);

  for i =1:N+1
    xtn1(i)=population(tps1(i),0.1,a);
    xtn2(i)=population(tps2(i),0.5,a);
    xtn3(i)=population(tps3(i),1.1,a);
    xtn4(i)=population(tps4(i),1.5,a);
  end

  // stockage du pas de temps et de l'erreur correspondante
  DT(cpt)=deltat;

  erreur1(cpt)=max(abs(xtn1-xa1))
  erreur2(cpt)=max(abs(xtn2-xa2))
  erreur3(cpt)=max(abs(xtn3-xa3))
  erreur4(cpt)=max(abs(xtn4-xa4))

cpt=cpt+1;

end

{% endhighlight %}

Ici on affiche l'erreur en norme logarithmique calculée ci-dessus.

![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/2.jpg)
{:class="img-responsive"}
<!-- {: .full} -->

Si on a :
$$erreur(\Delta t) = A * {\Delta t}^p$$ pour un certain $$A$$ \\
et un certain $$p$$, alors :
^
$$log(erreur(\Delta t))= p * log(\Delta t) + log(A)$$.
Ainsi, si on trace $$log(erreur(\Delta t))$$ en fonction de $$log(\Delta t)$$, on obtient une droite de coefficient directeur $$p$$.

$$p$$ est appelé l'ordre du schéma.

Comme toutes les courbes d'erreurs sont parallèles à la courbe $$O({\Delta t}^2)$$ , elles ont la même pente de 2 donc la méthode du point milieu est d'ordre 2.

## Cas de deux populations avec interaction de type proie/prédateur

<!-- ### Analyse théorique -->

<!-- #### Question a -->
<!-- rajouter TEX -->

<!-- #### Question b -->
<!-- rajouter TEX -->

<!-- #### Question c -->
<!-- rajouter TEX -->

### Implémentation de la fonction solution

<!-- #### Question a -->
<!-- voir CR -->

{% highlight matlab linenos %}

function y=F(t,x)
   y=zeros(2,1);
   y(1)=x(1)* (1-x(2));
   y(2)=x(2)* (-1+x(1));
endfunction

function y=H(x)
   y=(x(1)-log(x(1)))+(x(2)-log(x(2)));
endfunction

{% endhighlight %}

### Solutions graphiques

Solutions graphiques du système différentiel ordinaire (2) pour les schémas d'Euler explicite et du point milieu :

{% highlight matlab linenos %}

function [TPS,X]= EulerExplicite(f,t0,x0,dt,T)
   // initialisation
   time=t0;
   TPS=[t0];
   X=[x0];

   // boucle en temps
   while time<T
       // calcul de x_{n+1} et de t_{n+1}
       x0=x0+dt*f(time,x0); // le x0 de gauche joue le role de x_{n+1}, celui de droite celui de x_n
       time=time+dt;  

       // stockage des résultats
       TPS=[TPS, time];
       X=[X, x0];
end
TPS(:,$)=[];
X(:,$)=[];
endfunction

// Paramètres

T = 100;
deltat = 0.01;
N=T/deltat;
t0=0;

Xini=[1/4;3/4]

[TPSEE,XEE]= EulerExplicite(F,t0,Xini,deltat,T);
[TPSEM,XEM]= EulerModifie(F,t0,Xini,deltat,N);
{% endhighlight %}

![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/3.jpg)
{:class="img-responsive"}

### Schéma du point milieu

Le schéma du point milieu préserve mieux la périodicité (comme en atteste la figure précédente) que le schéma d'Euler explicite. En effet, la solution approchée par Euler, une fois implémentée dans la définition de la fonction $$\phi$$ (voir code) fait qu'elle n'est pas constante contrairement à ce qui a été démontré précédemment (et des changements de paramètres n'influent que très peu sur le résultat obtenu et rendent les calculs très coûteux, ce qui justifie le choix du changement de schéma).
Par contre en calculant $$\phi$$ par la méthode du point milieu, on obtient une valeur "quasi-constante" :

{% highlight matlab linenos %}
for j=1:size(XEE,2)
   phiEE(j)=H(XEE(:,j));
end

for j=1:size(XEM,2)
   phiEM(j)=H(XEM(:,j));
end

{% endhighlight %}

![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/4.jpg)
{:class="img-responsive"}

### Schéma de Crank-Nicolson

On retrace l'approximation des solutions par le schéma de Crank-Nicolson cette fois-ci :

{% highlight matlab linenos %}

//Schéma de Crank-Nicolson
function [t,x]=CrankNicolson(f,tini,xini,h,T)

if size(xini,1) < size(xini,2) then
   xini=xini'
end
       // initialisation
   t(1)=tini
   x(:,1)=xini
   time=tini;
   x0=xini;
   // boucle en temps
   while time<T
       // calcul de x_{n+1} et de t_{n+1}

       // définition de la fonction g telle que x_{n+1} est solution de g(x_{n+1})=0
       deff('[y]=g(x)','y=x-x0-(h/2)* (f(time,x)+f(time+h,x0))');
       x0=fsolve(x0, g); // le x0 de gauche joue le role de x_{n+1}, celui de droite celui de x_n
       time=time+h;
       // stockage des résultats
       t($+1)=time;
       x(:,$+1)=x0;
   end
endfunction
{% endhighlight %}

![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/5.jpg)
{:class="img-responsive"}

### Comparaison des méthodes

L'approximation des solutions est meilleure avec le nouveau schéma. L'erreur faite sur la valeur constante de $$\phi$$ est infime par rapport à celle du point milieu avec le schéma de Crank-Nicolson, même avec T=5000 :

{% highlight matlab linenos %}

// Paramètres
T = 5000;
deltat = 0.1;
t0=0;

Xini=[1/4;3/4]

[TPSCN,XCN]= CrankNicolson(F,t0,Xini,deltat,T);

{% endhighlight %}

![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/6.jpg)
{:class="img-responsive"}
