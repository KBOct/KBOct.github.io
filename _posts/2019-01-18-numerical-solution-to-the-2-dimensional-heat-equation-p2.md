---
title: "Numerical solution to the 2-dimensional heat equation (French)"
date: 2019-01-19
layout: splash
categories: [numerical analysis, python 2]
tags: [numerical analysis, pde, finite elements, python]
header:
  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Second part of my series on the Numerical solution to the 2-dimensional heat equation using the finite elements method in Python"
mathjax: true
---
# INTRO PARTIE 2

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (5).jpg)
{:class="img-responsive"}

<a id='q5'></a>
# QUESTION 5
[Prochaine question](#q6){: .btn .btn--success}

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (6).jpg)
{:class="img-responsive"}

<a id='q6'></a>
# QUESTION 6
[Question précédente](#q5){: .btn .btn--info}
[Prochaine question](#q7){: .btn .btn--success}
[Revenir en haut](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (7).jpg)
{:class="img-responsive"}

$$\forall x, y \in \mathbb{R}^2$$,

$$p(x,y)=\alpha x + \beta y + \gamma$$

$$ \Rightarrow \left\{ \begin{array}{l}
p(\hat{x_0},\hat{y_0})=\alpha \hat{x_0} + \beta \hat{y_0} + \gamma \\
p(\hat{x_1},\hat{y_1})=\alpha \hat{x_1} + \beta \hat{y_1} + \gamma \\
p(\hat{x_2},\hat{y_2})=\alpha \hat{x_2} + \beta \hat{y_2} + \gamma \end{array}
\right.$$

or $$(\hat{x_0},\hat{y_0}), (\hat{x_1},\hat{y_1})$$ et $$(\hat{x_2},\hat{y_2})$$ sont les sommets d'un triangle : ils sont donc linéairement indépendants.

Le système $$(S)$$ admet ainsi une unique solution, il est donc de Cramér.

La matrice du système est :

$$M = \left(
\begin{array}{* {9}c}
\hat{x_0} & \hat{y_0} & 1 \\
\hat{x_1} & \hat{y_1} & 1 \\
\hat{x_2} & \hat{y_2} & 1
\end{array}
\right)$$

Par les formules de Cramér on a :

$$\alpha = \frac{1}{det(M)} \vert \left(
\begin{array}{* {9}c}
p(\hat{x_0},\hat{y_0}) & \hat{y_0} & 1 \\
p(\hat{x_1},\hat{y_1}) & \hat{y_1} & 1 \\
p(\hat{x_2},\hat{y_2}) & \hat{y_2} & 1
\end{array}
\right) \vert$$

et
$$\beta = \frac{1}{det(A)} \vert \left(
\begin{array}{* {9}c}
\hat{x_0} & p(\hat{x_0},\hat{y_0}) & 1 \\
\hat{x_1} & p(\hat{x_1},\hat{y_1}) & 1 \\
\hat{x_2} & p(\hat{x_2},\hat{y_2}) & 1
\end{array}
\right) \vert$$


Puis :

$$\nabla p = \left(
\begin{array}{* {2}c}
\frac{\partial}{\partial x} (\alpha x + \beta y + \gamma) \\
\frac{\partial}{\partial y} (\alpha x + \beta y + \gamma)
\end{array}
\right)$$

$$\Rightarrow \nabla p = \left(
\begin{array}{* {2}c}
\alpha \\
\beta
\end{array}
\right)$$


{: .text-center}

<a id='q7'></a>
# QUESTION 7
[Question précédente](#q6){: .btn .btn--info}
[Prochaine question](#q8){: .btn .btn--success}
[Revenir en haut](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (8).jpg)
{:class="img-responsive"}

Notons $$A=(\hat{x_0},\hat{y_0})$$, $$B=(\hat{x_1},\hat{y_1})$$, $$C=(\hat{x_2},\hat{y_2})$$.

Si on pose $$D$$ tel que :

$$\overrightarrow{AB}+\overrightarrow{AC}=\overrightarrow{BD}$$

alors $$Aire(ABCD)=2\ Aire(ABC)=2\ \vert K \vert$$

or $$Aire(ABCD)=\vert det(M) \vert$$ (où $$M$$ est la matrice de la question 6)

D'où :

$$\vert K \vert = \frac{1}{2} \vert det(M) \vert$$

$$\Rightarrow \vert K \vert = \frac{1}{2} \vert det\left(
\begin{array}{* {9}c}
\hat{x_0} & \hat{y_0} & 1 \\
\hat{x_1} & \hat{y_1} & 1 \\
\hat{x_2} & \hat{y_2} & 1
\end{array}
\right) \vert$$

<a id='q8'></a>
# QUESTION 8
[Question précédente](#q7){: .btn .btn--info}
[Prochaine question](#q9){: .btn .btn--success}
[Revenir en haut](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (9).jpg)
{:class="img-responsive"}



<a id='q9'></a>
# QUESTION 9
[Question précédente](#q8){: .btn .btn--info}
[Prochaine question](#q10){: .btn .btn--success}
[Revenir en haut](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (10).jpg)
{:class="img-responsive"}

```python
              def f(x,y):
                  return 10

              def Matrices_Chaleur_Stationnaire(x,y,triangle,sb,f):

                  n=len(x)
                  Nt=len(triangle)
                  F=zeros(n)
                  Rigidite=zeros([n,n])

                  for i in range(Nt):

                      t0=int(triangle[i,0])
                      t1=int(triangle[i,1])
                      t2=int(triangle[i,2])

                      detd = (x[t1]-x[t0])*(y[t2]-y[t0]) - (x[t2]-x[t0])*(y[t1]-y[t0])
                      deta1 = y[t1] - y[t2]
                      detb1 = x[t2] - x[t1]
                      deta2 = y[t2] - y[t0]
                      detb2 = x[t0] - x[t2]
                      deta3 = y[t0] - y[t1]
                      detb3 = x[t1] - x[t0]

                      Aire = (1./2)*abs(detd)

                      Rigidite[triangle[i,0],triangle[i,0]]   =   Rigidite[triangle[i,0],triangle[i,0]]+(1/detd)*(1/detd)*(Aire) * (deta1*deta1 + detb1*detb1)
                      Rigidite[triangle[i,1],triangle[i,1]]   =   Rigidite[triangle[i,1],triangle[i,1]]+(1/detd)*(1/detd)*(Aire) * (deta2*deta2 + detb2*detb2)
                      Rigidite[triangle[i,2],triangle[i,2]]   =   Rigidite[triangle[i,2],triangle[i,2]]+(1/detd)*(1/detd)*(Aire) * (deta3*deta3 + detb3*detb3)
                      Rigidite[triangle[i,0],triangle[i,1]]   =   Rigidite[triangle[i,0],triangle[i,1]]+(1/detd)*(1/detd)*(Aire) * (deta1*deta2 + detb1*detb2)
                      Rigidite[triangle[i,0],triangle[i,2]]   =   Rigidite[triangle[i,0],triangle[i,2]]+(1/detd)*(1/detd)*(Aire) * (deta1*deta3 + detb1*detb3)
                      Rigidite[triangle[i,1],triangle[i,2]]   =   Rigidite[triangle[i,1],triangle[i,2]]+(1/detd)*(1/detd)*(Aire) * (deta2*deta3 + detb2*detb3)

                      Rigidite[triangle[i,1],triangle[i,0]]   =   Rigidite[triangle[i,0],triangle[i,1]]
                      Rigidite[triangle[i,2],triangle[i,0]]   =   Rigidite[triangle[i,0],triangle[i,2]]
                      Rigidite[triangle[i,2],triangle[i,1]]   =   Rigidite[triangle[i,1],triangle[i,2]]


                  for k in range(n):

                      for i in range(Nt):
                          somme = 0
                          for j in range(3):

                              if(triangle[i,j]==k):
                                      t0=triangle[i,0]
                                      t1=triangle[i,1]
                                      t2=triangle[i,2]
                                      detd = (x[t1]-x[t0])*(y[t2]-y[t0]) - (x[t2]-x[t0])*(y[t1]-y[t0])
                                      Aire = (1./2)*abs(detd)
                                      somme = f(x[triangle[i,j]], y[triangle[i,j]]) * Aire/3

                          F[k]=F[k]+somme

                  for i in sb:
                      Rigidite[i,:]=0
                      Rigidite[i,i]=1
                      F[i]=0

                  return [Rigidite,F]
```

<a id='q10'></a>
# QUESTION 10
[Question précédente](#q9){: .btn .btn--info}
[Prochaine question](#q11){: .btn .btn--success}
[Revenir en haut](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (11).jpg)
{:class="img-responsive"}


```python
              def Chaleur_Stationnaire(x,y,triangle,sb,f):

                  [Rigidite,F] = Matrices_Chaleur_Stationnaire(x,y,triangle,sb,f)
                  U=linalg.solve(Rigidite,F)

                  fig = plt.figure()
                  ax = fig.gca(projection='3d')
                  ax.plot_trisurf(x,y,triangle,U,cmap=cm.jet,linewidth=0.1)
                  plt.show()

                  return U
```
<a id='q11'></a>
# QUESTION 11
[Question précédente](#q10){: .btn .btn--info}
[Prochaine question](#q12){: .btn .btn--success}
[Revenir en haut](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (12).jpg)
{:class="img-responsive"}

```python
            ## CHALEUR STATIONNAIRE AVEC MAILLAGE RECTANGULAIRE
            ##
            [x,y,triangle,sb]=maillage_rectangle(3.,2.,20,20)
            U = Chaleur_Stationnaire(x,y,triangle,sb,f)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 11.png)
{:class="img-responsive"}

<a id='q12'></a>
# QUESTION 12
[Question précédente](#q11){: .btn .btn--info}
[Revenir en haut](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (13).jpg)
{:class="img-responsive"}

```python
              ## CHALEUR STATIONNAIRE AVEC MAILLAGE PIECE
              ##
              [x,y,triangle,sb]=importe_maillage("piece.dat")
              U = Chaleur_Stationnaire(x,y,triangle,sb,f)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 12.png)
{:class="img-responsive"}
