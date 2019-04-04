---
title: "Numerical solution to the 2-dimensional heat equation (French) - Part 1"
date: 2019-01-19
layout: splash
categories: [numerical analysis, python 2]
tags: [numerical analysis, pde, finite elements, python]
header:
  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Here, I compute the Numerical solution to the 2-dimensional heat equation using the finite elements method in Python"
mathjax: true
---
![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (0).jpg)
{:class="img-responsive"}
<a id='q1'></a>
[Prochaine question](#q2){: .btn .btn--success}
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
[Question précédente](#q1){: .btn .btn--info}
[Prochaine question](#q3){: .btn .btn--success}
[Revenir en haut](#link){: .btn .btn--warning }

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
[Question précédente](#q2){: .btn .btn--info}
[Prochaine question](#q4){: .btn .btn--success}
[Revenir en haut](#link){: .btn .btn--warning }

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
[Question précédente](#q3){: .btn .btn--info}
[Revenir en haut](#link){: .btn .btn--warning }

![alt]({{ site.url }}{{ site.baseurl }}/images/2 - heat equation/ANEDP (4).jpg)
{:class="img-responsive"}

Les fichiers de maillage lus par `importe_maillage` sont des tableaux contenant les coordonnées des sommets des triangles du maillage (à $$3$$ colonnes et $$N_t$$ lignes)
