

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

##################################################################################
#############################  CHALEUR STATIONNAIRE  #############################
##################################################################################

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


```python
##################################################################################
############################# CHALEUR INSTATIONNAIRE  ############################
##################################################################################



## \\\\\\\\\\\\\\\\\\\\\\\\\\\ VECTEUR INITIAL \\\\\\\\\\\\\\\\\\\\\\\\\\\
def vecteur_initial_U0(x,y):
    if(sqrt(x*x+y*y) <= 0.5):
        return 10
    else:
        return 0

def vecteur_initial_U0(x,y):
    return 0
## \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\




## \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\ FONCTION f \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
def f_temps(x,y,t):
    return 0

#def f_temps(x,y,t):
#    return 1

#def f_temps(x,y,t):
#    return 10

#def f_temps(x,y,t):
#    if (t <= 1):
#        return 1
#    else:
#        return 0
## \\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\


```


```python
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


```python
def importe_maillage(nom_fichier):
    with open(nom_fichier,'r') as fm:
        data=fm.readline().split()
        Ns=int(data[0])
        Nt=int(data[1])
        Nsb=int(data[2])
        x=zeros(Ns)
        y=zeros(Ns)
        triangle=zeros((Nt,3))
        sb=zeros(Nsb)
        for i in range(Ns):
            data=fm.readline().split()
            x[i]=float(data[0])
            y[i]=float(data[1])
        for i in range(Nt):
            data=fm.readline().split()
            triangle[i,:]=[int(data[0]),int(data[1]),int(data[2])]
        for i in range(Nsb):
            data=fm.readline().split()
            sb[i]=int(data[0])
        return [x,y,triangle,sb]


def matrice_M(x,y,triangle,sb):

    n=len(x)
    Nt=len(triangle)
    M=zeros([n,n])
    AireT=zeros(Nt)

    for i in range(Nt):
        t0=triangle[i,0]
        t1=triangle[i,1]
        t2=triangle[i,2]

        detd = (x[t1]-x[t0])*(y[t2]-y[t0]) - (x[t2]-x[t0])*(y[t1]-y[t0])

        Aire = (1./2)*abs(detd)

        M[triangle[i,0],triangle[i,0]]   =   M[triangle[i,0],triangle[i,0]]+(Aire * (1./6))
        M[triangle[i,1],triangle[i,1]]   =   M[triangle[i,1],triangle[i,1]]+(Aire * (1./6))
        M[triangle[i,2],triangle[i,2]]   =   M[triangle[i,2],triangle[i,2]]+(Aire * (1./6))
        M[triangle[i,0],triangle[i,1]]   =   M[triangle[i,0],triangle[i,1]]+(Aire * (1./12))
        M[triangle[i,0],triangle[i,2]]   =   M[triangle[i,0],triangle[i,2]]+(Aire * (1./12))
        M[triangle[i,1],triangle[i,2]]   =   M[triangle[i,1],triangle[i,2]]+(Aire * (1./12))

        M[triangle[i,1],triangle[i,0]]   =   M[triangle[i,0],triangle[i,1]]
        M[triangle[i,2],triangle[i,0]]   =   M[triangle[i,0],triangle[i,2]]
        M[triangle[i,2],triangle[i,1]]   =   M[triangle[i,1],triangle[i,2]]

    return M


def matrice_rigidite(x,y,triangle,sb):

    n=len(x)
    Nt=len(triangle)
    Rigidite=zeros([n,n])
    AireT=zeros(Nt)
    for i in range(Nt):

        t0=triangle[i,0]
        t1=triangle[i,1]
        t2=triangle[i,2]


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

    for i in sb:
        Rigidite[i,:]=0
        Rigidite[i,i]=1

    return Rigidite



def calcul_F(x,y,triangle,sb,f_temps,t):

    n=len(x)
    F = zeros(n)
    Nt=len(triangle)

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
                        somme = f_temps(x[triangle[i,j]], y[triangle[i,j]],t) * Aire/3

            F[k]=F[k]+somme

    for i in sb:
        F[i] = 0

    return F


def matrice_Q(x,y,triangle,sb,deltat):

    Rigidite = matrice_rigidite(x,y,triangle,sb)
    M = matrice_M(x,y,triangle,sb)

    for i in sb:
        M[i,:]=0
        M[i,i]=1

    Q = (1/deltat) * M + Rigidite

    return Q

def vecteur_L(x,y,triangle,sb,U,t,deltat):

    F = calcul_F(x,y,triangle,sb,f_temps,t)
    M = matrice_M(x,y,triangle,sb)

    L = (1/deltat) * (M.dot(U)) + F

    for i in sb:
        L[i]=0

    return L

```


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

############### DECOMENTER POUR VOIR LES VALEURS DE U L ET Q AU COURS DU PROGRAMME ###############

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








```python
####################################### QUESTION 11 ##############################################

## CHALEUR STATIONNAIRE AVEC MAILLAGE RECTANGULAIRE
##
[x,y,triangle,sb]=maillage_rectangle(3.,2.,20,20)
U = Chaleur_Stationnaire(x,y,triangle,sb,f)
```


```python
####################################### QUESTION 12 ##############################################

## CHALEUR STATIONNAIRE AVEC MAILLAGE PIECE
##
[x,y,triangle,sb]=importe_maillage("piece.dat")
U = Chaleur_Stationnaire(x,y,triangle,sb,f)
```


```python
#################################### QUESTION 14 & 15 ############################################

## CHALEUR INSTATIONNAIRE AVEC MAILLAGE RECTANGULAIRE
##
[x,y,triangle,sb]=maillage_rectangle(3.,2.,20,20)
U = Chaleur_Instationnaire(x,y,triangle,sb,10.,2.0,f_temps,vecteur_initial_U0)


## Commentaire : On remarque que la fonction chaleur instationnaire converge vers la fonction stationnaire a partir d'un certain rang.
```


```python
####################################### QUESTION 16 ##############################################

## CHALEUR INSTATIONNAIRE AVEC MAILLAGE DISQUE3
##
[x,y,triangle,sb]=importe_maillage("disque3.dat")
U = Chaleur_Instationnaire(x,y,triangle,sb,1.,0.25,f_temps,vecteur_initial_U0)


## Commentaire : On remarque que la chaleur se répartit de manière homogène sur toute la surface.
```




    IndexErrorTraceback (most recent call last)

    <ipython-input-22-02fe18b9843a> in <module>()
          4 ##
          5 [x,y,triangle,sb]=importe_maillage("disque3.dat")
    ----> 6 U = Chaleur_Instationnaire(x,y,triangle,sb,1.,0.25,f_temps,vecteur_initial_U0)
          7
          8


    <ipython-input-18-eb4e0bf1433a> in Chaleur_Instationnaire(x, y, triangle, sb, N, T, f_temps, vecteur_initial_U0)
          4     deltat = T/N
          5     U = zeros(Ns)
    ----> 6     Q = matrice_Q(x,y,triangle,sb,deltat)
          7
          8     for i in range(Ns):


    <ipython-input-17-d8d43d3f49d7> in matrice_Q(x, y, triangle, sb, deltat)
        124 def matrice_Q(x,y,triangle,sb,deltat):
        125
    --> 126     Rigidite = matrice_rigidite(x,y,triangle,sb)
        127     M = matrice_M(x,y,triangle,sb)
        128


    <ipython-input-17-d8d43d3f49d7> in matrice_rigidite(x, y, triangle, sb)
         65
         66
    ---> 67         detd = (x[t1]-x[t0])*(y[t2]-y[t0]) - (x[t2]-x[t0])*(y[t1]-y[t0])
         68         deta1 = y[t1] - y[t2]
         69         detb1 = x[t2] - x[t1]


    IndexError: only integers, slices (`:`), ellipsis (`...`), numpy.newaxis (`None`) and integer or boolean arrays are valid indices



```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```
