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

### H3 Heading

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



scilab code block with line numbers:
{% highlight matlab linenos %}
//#####################################################
//Cas d'une seule population : l'équation logistique
//#####################################################

//Question b)

clear   // efface toutes les variables

//Définition de la fonction de population solution non constante de l'EDO (1), obtenue par résolution analytique
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

xe1=population(t,0.1,a)
[tps1,xa1]= EulerModifie(f,0,0.1,deltat,N);
xe2=population(t,0.5,a)
[tps2,xa2]= EulerModifie(f,0,0.5,deltat,N);
xe3=population(t,1.1,a)
[tps3,xa3]= EulerModifie(f,0,1.1,deltat,N);
xe4=population(t,1.5,a)
[tps4,xa4]= EulerModifie(f,0,1.5,deltat,N);


scf(1)
clf

plot(t,xconstante1,'k--')
plot(t,xconstante2,'ko')
plot(t,xe1,'b^')
plot(tps1, xa1, 'b')
plot(t,xe2,'r^')
plot(tps2, xa2, 'r')
plot(t,xe3,'m^')
plot(tps3, xa3, 'm')
plot(t,xe4,'c^')
plot(tps4, xa4, 'c')

ylabel('x')
xlabel('t')
title("Tracé des solutions et de leurs approximations")
legend("Solution constante égale à 1","Solution constante égale à 0","Solution exacte pour x(0)=0.1","Solution approchée pour x(0)=0.1", "Solution exacte pour x(0)=0.5","Solution approchée pour x(0)=0.5","Solution exacte pour x(0)=1.1","Solution approchée pour x(0)=1.1","Solution exacte pour x(0)=1.5","Solution approchée pour x(0)=1.5",[19,0.85])

//Question c)

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


// affichage de l'erreur en norme logarithmique. Si on a
// erreur(deltat)= A*deltat^p pour un certain A et un certain p, alors
// log(erreur(deltat))=p*log(deltat)+ log(A)
// ainsi si on trace log(erreur(deltat)) en fonction de log(deltat), on obtient une
// droite de coefficient directeur p. p est appelé l'ordre du schéma.
scf(2)
clf
plot(log(DT), log(erreur1), "b")
plot(log(DT), log(erreur2), "r")
plot(log(DT), log(erreur3), "m")
plot(log(DT), log(erreur4), "c")

plot(log(DT), log(DT.^2), 'k--')
plot(log(DT), log(DT.^3), 'k-')

ylabel('log(erreur)')
xlabel('log(deltat)')
title("Tracé des erreurs en échelle logarithmique", "log (deltat)", "log (erreur)")
legend("Erreur numérique pour x(0)=0.1", "Erreur numérique pour x(0)=0.5", "Erreur numérique pour x(0)=1.1", "Erreur numérique pour x(0)=1.5","log(deltat^2) - pente 2","log(deltat^3) - pente 3",2)




//#####################################################
// Cas de deux populations avec interaction de type proie/prédateur
//#####################################################

//-----------------------------------------------------
// Analyse numérique
//-----------------------------------------------------

//Question a)


function y=F(t,x)
   y=zeros(2,1);
   y(1)=x(1)* (1-x(2));
   y(2)=x(2)* (-1+x(1));
endfunction

function y=H(x)
   y=(x(1)-log(x(1)))+(x(2)-log(x(2)));
endfunction


//Question b)


function [TPS,X]= EulerExplicite(f,t0,x0,dt,T)
   // initialisation
   time=t0;
   TPS=[t0];
   X=[x0];

   // boucle en temps
   while time<T
       // calcul de x_{n+1} et de t_{n+1}
       x0=x0+dt*f(time,x0); // le x0 de gauche joue le role de x_{n+1}, celui de celui de x_n
       time=time+dt;  // on ne peut pas intervertir la mise à jour de time et de x0, pourquoi ?

       // stockage des résultats


       TPS=[TPS, time];
       X=[X, x0];


end
TPS(:,$)=[];
X(:,$)=[];
endfunction

// Changement de paramètres à cause d'erreurs générées par les paramètres de l'énoncé
T = 100;
deltat = 0.01;
N=T/deltat;
t0=0;

Xini=[1/4;3/4]

[TPSEE,XEE]= EulerExplicite(F,t0,Xini,deltat,T);
[TPSEM,XEM]= EulerModifie(F,t0,Xini,deltat,N);



// affichage des trajectoires
// dans la fenêtre 3

scf(3);
clf;

plot(XEE(1,:), XEE(2,:), "b-");
plot(XEM(1,:), XEM(2,:), "r--");

ylabel('x2')
xlabel('x1')
title("Tracé des solutions pour les schémas Euler explicite et point milieu")
legend("Euler explicite","point milieu",4)

//Question c)

for j=1:size(XEE,2)
   phiEE(j)=H(XEE(:,j));
end

for j=1:size(XEM,2)
   phiEM(j)=H(XEM(:,j));
end

scf(4);
clf;

plot(TPSEE, phiEE, "b-");
plot(TPSEM, phiEM, "r-");


ylabel('phi(t)')
xlabel('t')
title("Evolution de la fonction phi au cours du temps")
legend("phi via Euler explicite", "phi via le point milieu",4)

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
       x0=fsolve(x0, g); // le x0 de gauche joue le role de x_{n+1}, celui de celui de x_n
//        if size(x0,1) < size(x0,2) then
//            x0=x0'
//        end
       time=time+h;  // attention cette fois time doit être actualisé avant x0 pourquoi ?
       // stockage des résultats
       t($+1)=time;
       x(:,$+1)=x0;
   end
endfunction



//Question e)


// Changement de paramètres à cause d'erreurs générées par les paramètres de l'énoncé
T = 5000;
deltat = 0.1;
t0=0;

Xini=[1/4;3/4]

[TPSCN,XCN]= CrankNicolson(F,t0,Xini,deltat,T);

// affichage des trajectoires
// dans la fenêtre 5

scf(5);
clf;

plot(XCN(1,:), XCN(2,:), "m-");


ylabel('x2')
xlabel('x1')
title("Tracé des solutions pour le schéma de Crank-Nicolson")
legend("Crank-Nicolson",1)


for j=1:size(XCN,2)
   phiCN(j)=H(XCN(:,j));
end

scf(6);
clf;

plot(TPSEE, phiEE, "b-");
plot(TPSCN, phiCN, "k-");

ylabel('phi(t)')
xlabel('t')
title("Evolution de la fonction phi au cours du temps")
legend("phi via Euler explicite","phi via le schéma de Crank-Nicolson",1)


{% endhighlight %}
