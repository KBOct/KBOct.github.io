---
title: "Binomial option pricing in Python"
date: 2019-01-19
categories: [quantitative finance, pricing, python]
tags: [quantitative finance, binomial tree, CRR, Cox Ross Rubinstein, options, option pricing, python]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "In this article, I implement 2 versions of the Binomial option pricing model in Python"
mathjax: true
author_profile: false
---

# H1 Heading

The binomial options pricing model provides a generalizable numerical method for the valuation of options. It was first proposed by Cox, Ross and Rubinstein in their 1979 article "Option pricing: A simplified approach" published in the Journal of Financial Economics

The Cox-Ross-Rubinstein model is a discrete-time version of the Black-Scholes model. It considers only one risky asset whose price is $$Sn$$ at time $$n, 0 < n < N,$$ and a riskless asset whose return is $$г$$ over one period of time. Which means that $$S^0_n=(1+r)^n$$. The risky asset is modelled as follows: between two consecutive periods the relative price change is either $$u$$ or $$d$, with $$-1 < d < u$$ :
$$Sn(l+a)\left\{ \begin{array}{l}
x'\left(t\right)=ax\left(t\right)(1-x\left(t\right)) \\
x(0)=x^{ini} \end{array}
\right.$$

Where the initial stock price $$S_0$$ is given.

I will implement 2 versions of the binomial model to price both European Calls and Puts.

# First version

{% highlight python linenos %}
import numpy

def binom_tree_call(N,T,S0, sigma, r, K, array_out=False):
    #Init

    #If array_out is False, only the call price will be displayed,
    #If it is True, however, the call price, the stock price tree and the call price tree will be displayed

    # Let's initialize the parameters
    dt=T/N
    u= numpy.exp(sigma*numpy.sqrt(dt))
    d=1/u
    p=(numpy.exp(r*dt)-d)/(u-d)

    # Price tree
    price_tree=numpy.zeros([N+1,N+1])

    # Price at each node
    for i in range(N+1):
        for j in range(i+1):
            price_tree[j,i]=S0*(d**j)* (u**(i-j))

    # Option price at maturity N
    option=numpy.zeros([N+1,N+1])
    option[:, N]=numpy.maximum(numpy.zeros(N+1), price_tree[:, N]-K)

    # Option price at t = 0 by going backwards
    for i in numpy.arange(N-1,-1,-1):
        for j in numpy.arange(0,i+1):
            option[j,i]=numpy.exp(-r*dt)* (p*option[j ,i+1]+(1-p)* option[j+1,i+1])

    # Return
    if array_out:
        return [option[0,0], price_tree, option]
    else:
        return option[0,0]
{% endhighlight %}

````
binom_tree_call(50,1,100,0.1,0.05,100, True)

[6.783564165691306,
 array([[100.        , 101.42426087, 102.86880693, ..., 197.15548787,
         199.96349633, 202.81149816],
        [  0.        ,  98.59573946, 100.        , ..., 191.65721247,
         194.38691115, 197.15548787],
        [  0.        ,   0.        ,  97.2111984 , ..., 186.31227307,
         188.96584587, 191.65721247],
        ...,
        [  0.        ,   0.        ,   0.        , ...,  50.72138802,
          51.4437929 ,  52.17648671],
        [  0.        ,   0.        ,   0.        , ...,   0.        ,
          50.00912758,  50.72138802],
        [  0.        ,   0.        ,   0.        , ...,   0.        ,
           0.        ,  49.30686914]]),
 array([[  6.78356417,   7.72801529,   8.74811248, ...,  97.355288  ,
         100.06344635, 102.81149816],
        [  0.        ,   5.72515965,   6.58569481, ...,  91.85701261,
          94.48686117,  97.15548787],
        [  0.        ,   0.        ,   4.75982223, ...,  86.5120732 ,
          89.06579589,  91.65721247],
        ...,
        [  0.        ,   0.        ,   0.        , ...,   0.        ,
           0.        ,   0.        ],
        [  0.        ,   0.        ,   0.        , ...,   0.        ,
           0.        ,   0.        ],
        [  0.        ,   0.        ,   0.        , ...,   0.        ,
           0.        ,   0.        ]])]

````

TEXTE

# Alternate faster version

This is a faster version using the numba Just-In-Time compiling library available at
[link](https://numba.pydata.org/)


{% highlight python linenos %}
import numba

{% endhighlight %}
