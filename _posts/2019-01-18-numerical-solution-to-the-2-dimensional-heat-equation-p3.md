---
title: "Numerical solution to the 2-dimensional heat equation (French)"
date: 2019-01-19
layout: splash
categories: [numerical analysis, python 2]
tags: [numerical analysis, pde, finite elements, python]
header:
  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Third and last part of the Numerical solution to the 2-dimensional heat equation series"
mathjax: true
---
# INTRO PARTIE 3

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (14).jpg)
{:class="img-responsive"}

<a id='q13'></a>
# QUESTION 13
[Prochaine question](#q14){: .btn .btn--success}

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (15).jpg)
{:class="img-responsive"}

```python
              def Chaleur_Instationnaire(x,y,triangle,sb,N,T,f_temps,vecteur_initial_U0):

                  Ns = len(x)
                  deltat = T/N
                  U = zeros(Ns)
                  Q = matrice_Q(x,y,triangle,sb,deltat)

                  for i in range(Ns):
                      U[i]=vecteur_initial_U0(x[i],y[i])
              ##dot
                  plt.ion()
                  fig = plt.figure()
                  ax = fig.gca(projection='3d')


                  for i in range(int(N)):
                      t=(i+1)*deltat
                      print(t)
                      L = vecteur_L(x,y,triangle,sb,U,t,deltat)
                      U = linalg.solve(Q,L)

                      print("\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\  ETAPE %d \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\:" % i)
                      print("Vecteur U à l'étape %d :" % i)
                      print(U)
                      print("Vecteur L à l'étape %d :" % i)
                      print(L)
                      print("Matrice Q à l'étape %d :" % i)
                      print(Q)

              ##################################################################################################
                      ax.cla()

              ############################# CHANGE L'AXE DES ORDONNEES DU GRAPHIQUE ############################
              #        ax.set_zlim(0,0.1)
                      ax.set_zlim(0,5)
              #        ax.set_zlim(0,10)
              ##################################################################################################
                      ax.plot_trisurf(x,y,triangle,U,cmap=cm.jet,linewidth=0.1)
                      plt.pause(1e-6)

                  return U
```

<a id='q14'></a>
# QUESTION 14
[Question précédente](#q13){: .btn .btn--info}
[Prochaine question](#q15){: .btn .btn--success}
[Revenir en haut](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (16).jpg)
{:class="img-responsive"}

```python
              ## CHALEUR INSTATIONNAIRE AVEC MAILLAGE RECTANGULAIRE
              ##
              def vecteur_initial_U0(x,y):
                  return 0

              def f_temps(x,y,t):
                  return 10

              [x,y,triangle,sb]=maillage_rectangle(3.,2.,20,20)
              U = Chaleur_Instationnaire(x,y,triangle,sb,10.,0.1,f_temps,vecteur_initial_U0)


              ## Commentaire : On remarque que la fonction chaleur instationnaire converge vers la fonction stationnaire a partir d'un certain rang.
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 14 (T valant 0.1).png)
{:class="img-responsive"}

```python
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,0.5,f_temps,vecteur_initial_U0)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 14 (T valant 0.5).png)
{:class="img-responsive"}

```python
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,1.0,f_temps,vecteur_initial_U0)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 14 (T valant 1.0).png)
{:class="img-responsive"}

```python
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,1.5,f_temps,vecteur_initial_U0)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 14 (T valant 1.5).png)
{:class="img-responsive"}

```python
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,2.0,f_temps,vecteur_initial_U0)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 14 (T valant 2.0).png)
{:class="img-responsive"}


<a id='q15'></a>
# QUESTION 15
[Question précédente](#q14){: .btn .btn--info}
[Prochaine question](#q16){: .btn .btn--success}
[Revenir en haut](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (17).jpg)
{:class="img-responsive"}

```python
              ## CHALEUR INSTATIONNAIRE AVEC MAILLAGE RECTANGULAIRE
              ##

              def f_temps(x,y,t):
                  if (t <= 1):
                      return 1
                  else:
                      return 0

              [x,y,triangle,sb]=maillage_rectangle(3.,2.,20,20)
              U = Chaleur_Instationnaire(x,y,triangle,sb,10.,0.1,f_temps,vecteur_initial_U0)

              ## Commentaire : On remarque que la fonction chaleur instationnaire converge vers la fonction stationnaire a partir d'un certain rang.
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 15 (T valant 0.1).png)
{:class="img-responsive"}

```python
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,0.5,f_temps,vecteur_initial_U0)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 15 (T valant 0.5).png)
{:class="img-responsive"}

```python
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,1.0,f_temps,vecteur_initial_U0)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 15 (T valant 1.0).png)
{:class="img-responsive"}

```python
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,1.5,f_temps,vecteur_initial_U0)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 15 (T valant 1.5).png)
{:class="img-responsive"}

<a id='q16'></a>
# QUESTION 16
[Question précédente](#q15){: .btn .btn--info}
[Revenir en haut](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (18).jpg)
{:class="img-responsive"}

```python
def vecteur_initial_U0(x,y):
    if(sqrt(x*x+y*y) <= 0.5):
        return 10
    else:
        return 0

def f_temps(x,y,t):
        return 0

[x,y,triangle,sb]=maillage_rectangle(3.,2.,20,20)
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,0.0,f_temps,vecteur_initial_U0)
```
![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 16 - 1 (T valant 0).png)
{:class="img-responsive"}

```python
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,0.01,f_temps,vecteur_initial_U0)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 16 - 2 (T valant 0.01).png)
{:class="img-responsive"}

```python
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,0.05,f_temps,vecteur_initial_U0)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 16 - 3 (T valant 0.05).png)
{:class="img-responsive"}

```python
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,0.1,f_temps,vecteur_initial_U0)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 16 - 4 (T valant 0.1).png)
{:class="img-responsive"}

```python
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,0.25,f_temps,vecteur_initial_U0)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 16 - 5 (T valant 0.25).png)
{:class="img-responsive"}

```python
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,0.5,f_temps,vecteur_initial_U0)
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 16 - 6 (T valant 0.5).png)
{:class="img-responsive"}
