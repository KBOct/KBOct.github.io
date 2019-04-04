---
title: "Numerical solution to the 2-dimensional heat equation (French)"
date: 2019-01-19
layout: splash
categories: [numerical analysis, python 2]
tags: [numerical analysis, pde, finite elements, python]
header:
  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Here, I compute the Numerical solution to the 2-dimensional heat equation using the finite elements method in Python"
mathjax: true
---
<a id='q1'></a>
# INTRO PARTIE 1
[Next question](#q2){: .btn .btn--success}
![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (1).jpg)
{:class="img-responsive"}


```python
            from numpy import *
            from matplotlib.pyplot import *
            from random import *
            import matplotlib.pyplot as plt
            from mpl_toolkits.mplot3d import axes3d
            from matplotlib import cm

            import sys
            reload(sys)  
            sys.setdefaultencoding('Cp1252')

            ## //////////////////////////////////////////////////////////////////////////////////
            ## ####################################### QUESTION 1 ###############################
            ## //////////////////////////////////////////////////////////////////////////////////


            def maillage_rectangle(Lx,Ly,Nx,Ny):

                dX=(Lx/Nx)
                dY=(Ly/Ny)

                n=(Nx+1)*(Ny+1)
                Nt=2*Nx*Ny

                x = zeros([n])
                y = zeros([n])
                triangle=zeros([Nt,3])
                sb=zeros((Nx+Ny)*2)


                a=0
                for i in range(Nx+1):
                    for j in range(Ny+1):
                        s=i+(Nx+1)*j
                        x[s]=i*dX
                        y[s]=j*dY
                        if(i==0 or i==Nx or j==0 or j==Ny):
                            sb[a]=s
                            a=a+1

                for i in range(Nx):
                    for j in range(Ny):
                        s=i+(Nx+1)*j
                        t1=2*(Nx*j + i)
                        t2=t1+1

                        triangle[t1,0]=s
                        triangle[t1,1]=s+Nx+1
                        triangle[t1,2]=s+Nx+2
                        triangle[t2,0]=s
                        triangle[t2,1]=s+1
                        triangle[t2,2]=s+Nx+2



                return[x,y,triangle,sb]
```
<a id='q2'></a>
# QUESTION 2
[Next question](#q3){: .btn .btn--success}
[Previous question](#q1){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (2).jpg)
{:class="img-responsive"}


```python
              ## TRACE DU MAILLAGE RECTANGLE

              [x,y,triangle,sb]=maillage_rectangle(3.,2.,6,4)
              plt.gca().set_aspect('equal')
              plt.triplot(x,y,triangle)
              plt.show()
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 2.png)
{:class="img-responsive"}

<a id='q3'></a>
# QUESTION 3
[Next question](#q4){: .btn .btn--success}
[Previous question](#q2){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (3).jpg)
{:class="img-responsive"}



```python
              ## TRACE DU MAILLAGE PIECE

              [x,y,triangle,sb]=importe_maillage("piece.dat")
              plt.gca().set_aspect('equal')
              plt.triplot(x,y,triangle)
              plt.show()
```

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/image Question 3.png)
{:class="img-responsive"}

<a id='q4'></a>
# QUESTION 4
[Next question](#q5){: .btn .btn--success}
[Previous question](#q3){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (4).jpg)
{:class="img-responsive"}

Les fichiers de maillage lus par `importe_maillage` sont des tableaux contenant les coordonnées des sommets des traingles du maillage (à $$3$$ colonnes et $$N_t$$ lignes)


# INTRO PARTIE 2

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (5).jpg)
{:class="img-responsive"}

## **REPONSE ECRITE**

<a id='q5'></a>
# QUESTION 5
[Next question](#q6){: .btn .btn--success}
[Previous question](#q4){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (6).jpg)
{:class="img-responsive"}

## **REPONSE ECRITE**

<a id='q6'></a>
# QUESTION 6
[Next question](#q7){: .btn .btn--success}
[Previous question](#q5){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (7).jpg)
{:class="img-responsive"}

## **REPONSE ECRITE**

<a id='q7'></a>
# QUESTION 7
[Next question](#q8){: .btn .btn--success}
[Previous question](#q6){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (8).jpg)
{:class="img-responsive"}

## **REPONSE ECRITE**

<a id='q8'></a>
# QUESTION 8
[Next question](#q9){: .btn .btn--success}
[Previous question](#q7){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (9).jpg)
{:class="img-responsive"}

## **REPONSE ECRITE**

<a id='q9'></a>
# QUESTION 9
[Next question](#q10){: .btn .btn--success}
[Previous question](#q8){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

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
[Next question](#q11){: .btn .btn--success}
[Previous question](#q9){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

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
[Next question](#q12){: .btn .btn--success}
[Previous question](#q10){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

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
[Next question](#q13){: .btn .btn--success}
[Previous question](#q11){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

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


# INTRO PARTIE 3

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (14).jpg)
{:class="img-responsive"}

<a id='q13'></a>
# QUESTION 13
[Next question](#q14){: .btn .btn--success}
[Previous question](#q12){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

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
[Next question](#q15){: .btn .btn--success}
[Previous question](#q13){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

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
[Next question](#q16){: .btn .btn--success}
[Previous question](#q14){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

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
[Previous question](#q15){: .btn .btn--info}
[Back to top](#link){: .btn .btn--warning }

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
