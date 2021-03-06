---
title: "Numerical Analysis for Ordinary Differential Equations : Red Blood Cell agglomeration (French) - Part 3"
date: 2019-01-17
categories: [numerical analysis, scilab]
tags: [numerical analysis, ode, scilab]
header:
  image: "/images/1- numerical analysis for ODEs/RBC banner2.jpg"
excerpt: "Third and last part of the series, wherein the actual numerical scheme to model RBC agglomeration is introduced and implemented"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---

# Partie 3 : Modélisation des globules rouges

Dans cette partie, on s’intéresse à la modélisation du phénomène d’agglomération des globules rouges. Ces derniers vont être modélisés par des particules ponctuelles
interagissant par une force d’interaction liée à la distance entre deux globules rouges voisins.

## Description du modèle

On considère $$M+2$$ globules rouges (aussi appelés hématies), représentés par des particules ponctuelles placées sur l’intervalle $$[0, 1]$$. Chaque globule rouge subit l’influence de ses deux voisins, sauf les deux globules des extrémités, qui sont placés respectivement en $$0$$ et en $$1$$. On note $$x_i(t)$$ la position de l’hématie $$i$$ au temps $$t$$. L’indice varie entre $$0$$ et $$M + 1$$.

On a donc :

$$0 = x_0(t) < x_1(t) < ... < x_M(t) < x_{M+1}(t) = 1$$

La force d’interaction entre les particules est attractive lorsque la distance entre deux particules est assez grande, et décroit vers $$0$$ lorsque cette distance tend vers l’infini.

De plus, pour prendre en compte la taille des globules, on considère que la force d’interaction devient répulsive lorsque la distance entre les hématies devient inférieure à une valeur $$r$$, correspondant à la taille caractéristique (typiquement le rayon) de l’hématie.
^
Cette force d’interaction va être modélisée par une fonction $$I$$ de la distance entre les particules qui va donc tendre vers $$0$$ à l’infini, qui sera positive pour une distance $$d > r$$ et négative pour une distance $$d < r$$.

On choisit ici la fonction suivante :

$$I : \quad \begin{array}{cl}
\mathbb{R}_{+}&\to \quad \mathbb{R}\\
d  &\mapsto \quad (\frac{c}{d}) \ln  (\frac{d}{r})
\end{array}
$$


où $$c$$ est un paramètre qui caractérise l’intensité de la force.
Les forces exercées sur l’hématie $$i$$ comportent
donc la force exercée par l’hématie $$i + 1$$, qui est $$I(x_{i+1} - x_i)$$, la force exercée par l’hématie $$i − 1$$, qui est $$−I(x_i − x_{i−1})$$, ainsi qu’une force de frottement qui est proportionnelle à la vitesse de l’hématie, et s’oppose au mouvement de la particule : $$−\alpha \dot{x_i}$$, où $$\alpha$$ est appelé coefficient de frottement.

Les équations du mouvement associées à ce système s’écrivent donc :

$$ \forall \ i \ = 1,\dots, M,  \left\{ \begin{array}{l}
m \ddot{x_i} = I(x_{i+1} - x_i) - I(x_i - x_{i-1}) -\alpha \dot{x_i}\ \\
x_i \left(0\right)=x^{ini}_i \\
x^\prime_i \left(0\right)=v^{ini}_i \end{array}
\right.$$

où $$m$$ est la masse d’un globule rouge (prise égale à $$1$$ dans la suite).


## Schéma numérique de base

On souhaite simuler numériquement le modèle précédent. On commence par définir $$T$$ le temps final de la simulation. On discrétise l’intervalle $$[0, T]$$ en $$K$$ intervalles égaux $$[t_k, t_{k+1}]$$ pour $$k$$ allant de $$0$$ à $$K − 1$$ avec $$t_k = kh$$, et $$h = \frac{T}{K}$$.
On s’intéresse au schéma suivant ($$k$$ désigne un entier positif ou nul)

$$\left\{ \begin{array}{l}
x^0_i=\ x^{ini}_i \\
x^1_i=\ x^0_i+\Delta tv^{ini}_i \\
w^k_i=I\left(x^k_{i+1}-x^k_i\right)-I\left(x^k_i-x^k_{i-1}\right) \\
x^{k+1}_i={2x}^k_i-x^{k-1}_i+{\Delta t}^2w^k_i-\alpha \Delta t(x^k_i-x^{k-1}_i) \end{array}
\right.$$

On utilisera les paramètres suivants : $$K = 1500, \Delta t = 0.002, \alpha = 10, c = 0.5, r = 0.004$$.

### Equations du schéma numérique pour $$M=3$$

Nous allons nous interesser au cas particulier du modèle restreint à $$3$$ hématies $$(M =3)$$. Les équations sont donc les suivantes :

$$\left\{ \begin{array}{l}
x^0_1=\ x^{ini}_1 \\
x^1_1=\ x^0_1+\Delta tv^{ini}_1 \\
w^k_1=I\left(x^k_2-x^k_1\right)-I\left(x^k_1-x^k_0\right) \\
x^{k+1}_1={2x}^k_1-x^{k-1}_1+{\Delta t}^2w^k_1-\alpha \Delta t(x^k_1-x^{k-1}_1) \end{array}
\right.$$

$$\left\{ \begin{array}{l}
x^0_2=\ x^{ini}_2 \\
x^1_2=\ x^0_2+\Delta tv^{ini}_2 \\
w^k_2=I\left(x^k_3-x^k_2\right)-I\left(x^k_2-x^k_1\right) \\
x^{k+1}_2={2x}^k_2-x^{k-1}_2+{\Delta t}^2w^k_2-\alpha \Delta t(x^k_2-x^{k-1}_2) \end{array}
\right.$$

$$\left\{ \begin{array}{l}
x^0_3=\ x^{ini}_3 \\
x^1_3=\ x^0_3+\Delta tv^{ini}_3 \\
w^k_3=I\left(x^k_4-x^k_3\right)-I\left(x^k_3-x^k_2\right) \\
x^{k+1}_3={2x}^k_3-x^{k-1}_3+{\Delta t}^2w^k_3-\alpha \Delta t(x^k_3-x^{k-1}_3) \end{array}
\right.$$

### Implémentation du schéma dans le cas où $$M = 3$$

On commencera par implémenter la fonction I, puis on codera une fonction TroisGlobules qui prend en argument, outre les paramètres définis
plus haut, les données initiales colonnes $$x^{ini}$$ et $$v^{ini}$$, et qui renvoie comme résultats la discrétisation en temps $$TPS$$ et le tableau suivant :
<!-- \setcounter{MaxMatrixCols}{21} -->

$$TRAJ = \left(
\begin{array}{* {21}c}
x^0_1 & x^1_1 & x^2_1 & \dots & x^k_1 & \dots & x^N_1 \\
x^0_2 & x^1_2 & x^2_2 & \dots & x^k_2 & \dots & x^N_2 \\
x^0_3 & x^1_3 & x^2_3 & \dots & x^k_3 & \dots & x^N_3
\end{array}
\right)$$

{% highlight matlab linenos %}
// Définition de la fonction I représentant la force d'interaction entre deux globules en fonction de leur distance d

function y=I(d)
    if d > 0 then
        y=(0.5/d) * log(d/0.004);
    else
        disp("la variable d doit être strictement positive !");
    end
endfunction


// Définition de la fonction TroisGlobules

function [TPS,TRAJ]=TroisGlobules(K,deltat,alpha,xini,vini)
// Positions des globules aux extremités x0 et x4 nécessaires pour les calculs concernant le globule 1 et le globule 3
    x0=0.0;
    x4=1.0;
// Temps final T
    T=K*deltat;
// Discretisation de [0,T] en K intervalles égaux
    TPS = linspace(0,T,K);
// Redimensionnement des vecteurs xini et vini si besoin
if size(xini,2)==3 then
    xini=xini';
end
if size(vini,2)==3 then
    vini=vini';
end
// Initialisation du tableau TRAJ
TRAJ = zeros(3,K);

// Calcul des 2 premières colonnes du tableau TRAJ
TRAJ(:,1)=xini;
TRAJ(:,2)=xini+(deltat*vini);


// Initialisation de la matrice w qui contiendra les forces qui s'exercent sur chaque globule
w=zeros(3,K);

//ATTENTION : le paramètre k considéré ici correspond au numéro de la colonne du tableau TRAJ et varie de 1 à K. De plus, on doit initialiser la boucle à partir de k=2 pour pouvoir calculer TRAJ(:,k-1) et on doit terminer cette boucle à k=K-1 pour rester cohérent avec la définition du tableau TRAJ. On remarquera que la matrice w calculée aura donc ses premières et dernières colonnes vides, ce qui n'est pas sans rapport avec le comportement particulier des globules aux extrémités

for k = 2:K-1
    // Calcul des forces qui s'exercent sur les globules
    w(1,k)=I(TRAJ(2,k)-TRAJ(1,k))-I(TRAJ(1,k)-x0);
    w(2,k)=I(TRAJ(3,k)-TRAJ(2,k))-I(TRAJ(2,k)-TRAJ(1,k));
    w(3,k)=I(x4-TRAJ(3,k))-I(TRAJ(3,k)-TRAJ(2,k));
// Reprise du calcul des colonnes du tableau TRAJ
    TRAJ(:,k+1)=(2*TRAJ(:,k))-TRAJ(:,k-1)+(deltat^2) * w(:,k)-(alpha*deltat) * (TRAJ(:,k)-TRAJ(:,k-1));
end

endfunction

K=30000;
deltat=0.0001;
alpha=10;
xini=[0.27,0.55,0.73];
vini=zeros(1,3)
//[TPS,TRAJ]=TroisGlobules(30000,0.0001,10,[0.27,0.55,0.73],[0,0,0]);
[TPS,TRAJ]=TroisGlobules(K,deltat,alpha,xini,vini);
{% endhighlight %}

 On trace l'évolution des positions des hématies au cours du temps avec les conditions initiales $$(x^{ini}_1,x^{ini}_2,x^{ini}_3)=(0,27; 0,55;0,73)$$ et $$(v^{ini}_1,v^{ini}_2,v^{ini}_3)=(0; 0;0)$$ :


![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/3-1.jpg)
{:class="img-responsive"}

Chacune des hématies s'agglomère à l'hématie qui est initialement la plus proche et les hématies agglomérées en forment une nouvelle qui aura le même comportement :

- L'hématie $$2$$ s'agglomère à l'hématie $$3$$ parce
qu'elles sont plus proches que la $$1$$ et la $$2$$ $$(x^0_3-x^0_2=0,18<x^0_2-x^0_1=0,28)$$ ou la $$3$$ et le globule à l'extrémité en position $$1$$ $$(x^0_3-x^0_2=0,18<1-x^0_3=0,27)$$

- Puis le globule aggloméré en position médiane au temps $$k, x^k_{2,3}\approx 0,64$$ est alors plus proche du globule à l'extrémité en position $$1$$ que du globule numéro $$1\ (1-x^k_{2,3}=0,36<0,37=x^k_{2,3}-x^0_1<x^k_{2,3}-x^k_1$$ : la dernière inégalité étant visible sur la figure au moment où les courbes des hématies $$2$$ et $$3$$ se rejoignent), il vient donc s'agglomérer au globule en position $$1$$

- L'hématie $$1$$ vient s'agglomérer au globule en position $$0$$ par un raisonnement analogue


Remarque : si les $$3$$ hématies ainsi que celles aux extrémités sont équidistantes au moment initial, i.e. $$(x^0_1,x^0_2,x^0_3)=(0,25;0,50;0,75)$$, alors elles restent immobiles en tout temps :

![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/3-1alt.jpg)
{:class="img-responsive"}


### Implémentation du schéma dans le cas où $$M = 40$$


{% highlight matlab linenos %}
function [TPS,TRAJ]=MGlobules(K,deltat,M,alpha,xini,vini)
// Positions des globules aux extremités x0 et x4 nécessaires pour les calculs concernant le globule 1 et le globule 3
    x0=0.0;
    xMplus1=1.0;
// Temps final T
    T=K*deltat;
// Discretisation de [0,T] en K intervalles égaux
    TPS = linspace(0,T,K);
// Redimensionnement des vecteurs xini et vini si besoin
if size(xini,2)==M then
    xini=xini';
end
if size(vini,2)==M then
    vini=vini';
end
// Initialisation du tableau TRAJ
TRAJ = zeros(M,K);

// Calcul des 2 premières colonnes du tableau TRAJ
TRAJ(:,1)=xini;
TRAJ(:,2)=xini+(deltat*vini);


// Initialisation de la matrice w qui contiendra les forces qui s'exercent sur chaque globule
w=zeros(M,K);

//ATTENTION : le paramètre k considéré ici correspond au numéro de la colonne du tableau TRAJ et varie de 1 à K contrairement au paramètre de l'énoncé qui varie de 0 à K-1. De plus, on doit initialiser la boucle à partir de k=2 pour pouvoir calculer TRAJ(:,k-1) et on doit terminer cette boucle à k=K-1 pour rester cohérent avec la définition du tableau TRAJ. On remarquera donc que la matrice w calculée aura donc ses premières et dernières colonnes vides, ce qui n'est pas sans rapport avec le comportement particulier des globules aux extrémités

for k = 2:K-1
    // Calcul des forces qui s'exercent sur les globules
    w(1,k)=I(TRAJ(2,k)-TRAJ(1,k))-I(TRAJ(1,k)-x0);
    for j = 2:M-1
        w(j,k)=I(TRAJ(j+1,k)-TRAJ(j,k))-I(TRAJ(j,k)-TRAJ(j-1,k));
    end
    w(M,k)=I(xMplus1-TRAJ(M,k))-I(TRAJ(M,k)-TRAJ(M-1,k));
// Reprise du calcul des colonnes du tableau TRAJ
    TRAJ(:,k+1)=(2*TRAJ(:,k))-TRAJ(:,k-1)+(deltat^2)* w(:,k)-(alpha*deltat)* (TRAJ(:,k)-TRAJ(:,k-1))
end

endfunction

K=30000;
deltat=0.0001;
alpha=10;
M=40;

rand("uniform");
nombres_aleatoires = rand(1,M) * 2-1;
xini_aleatoire = zeros(M,1);
viniM = zeros(M,1);
for i = 1:M
    xini_aleatoire(i)=(i+(0.1*nombres_aleatoires(i)))/(M+1);
end


[TPSM,TRAJM]=MGlobules(K,deltat,M,alpha,xini_aleatoire,viniM);

scf(3)
clf

plot(TPSM,TRAJM,'r-')

ylabel('Position','fontsize',2)
xlabel('Temps','fontsize',2)
title("Evolution des positions des hématies au cours du temps",'fontsize',2)

{% endhighlight %}


![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/3-2.jpg)
{:class="img-responsive"}
