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
The binomial options pricing model provides a generalizable numerical method for the valuation of options. It was first proposed by John Carrington Cox, Stephen Ross and Mark Edward Rubinstein in their 1979 article "Option pricing: A simplified approach" published in the *Journal of Financial Economics*

The Cox-Ross-Rubinstein model is a discrete-time version of the Black-Scholes model. It considers only one risky asset whose price is $$S_n$$ at time $$n, 0 < n < N,$$ and a riskless asset whose return is $$r$$ over one period of time.
Which means that $$S^0_n=(1+r)^n$$.

The risky asset is modelled as follows: between two consecutive periods the relative price change is either $$u$$ or $$d$$, with $$-1 < d < u$$ :

$$S_{n+1}\left\{ \begin{array}{l}
S_n \quad (1+u) \\
S_n(1+d) \end{array}
\right.$$

Where the initial stock price $$S_0$$ is given.

I will implement 2 versions of the binomial model to price both European Calls and Puts.

# First version

{% highlight python linenos %}
import numpy

def binom_tree_call_put(N, T, S0, sigma, r, K, call=True, array_out=False):
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
    if call:
        option[:, N]=numpy.maximum(numpy.zeros(N+1), price_tree[:, N]-K)
    else:
        option[:, N]=numpy.maximum(numpy.zeros(N+1), K-price_tree[:, N])

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

An execution of
{% highlight python %}
binom_tree_call_put(N=50, T=1, S0=100, sigma=0.2, r=0.06, K=99, call=True, array_out=True)
{% endhighlight %}
returns the following tree :
````
[11.546434850755071,
 array([[100.        , 102.86880693, 105.81991439, ..., 388.70286396,
         399.85399865, 411.32503788],
        [  0.        ,  97.2111984 , 100.        , ..., 367.32487093,
         377.86271228, 388.70286396],
        [  0.        ,   0.        ,  94.50017095, ..., 347.12263097,
         357.08090906, 367.32487093],
        ...,
        [  0.        ,   0.        ,   0.        , ...,  25.72659203,
          26.46463828,  27.22385766],
        [  0.        ,   0.        ,   0.        , ...,   0.        ,
          25.00912842,  25.72659203],
        [  0.        ,   0.        ,   0.        , ...,   0.        ,
           0.        ,  24.31167344]]),
 array([[ 11.54643485,  13.40897969,  15.46463735, ..., 289.94017906,
         300.9727274 , 312.32503788],
        [  0.        ,   9.60391567,  11.26670021, ..., 268.56218603,
         278.98144103, 289.70286396],
        [  0.        ,   0.        ,   7.86799392, ..., 248.35994607,
         258.19963781, 268.32487093],
        ...,
        [  0.        ,   0.        ,   0.        , ...,   0.        ,
           0.        ,   0.        ],
        [  0.        ,   0.        ,   0.        , ...,   0.        ,
           0.        ,   0.        ],
        [  0.        ,   0.        ,   0.        , ...,   0.        ,
           0.        ,   0.        ]])]

````
An execution of
{% highlight python %}
binom_tree_call_put(N=50, T=1, S0=100, sigma=0.2, r=0.06, K=99, call=True, array_out=False)
{% endhighlight %}
simply returns the first value:

````
11.546434850755071
````


# Alternate faster version

This is a faster version using the numba Just-In-Time compiling library available at
[https://numba.pydata.org/](https://numba.pydata.org/)

We just import the library and add a line to the previous function's definition:

{% highlight python linenos %}
import numba

@numba.jit
def binom_tree_call_put_faster(N, T, S0, sigma, r, K, call=True, array_out=False):
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
    if call:
        option[:, N]=numpy.maximum(numpy.zeros(N+1), price_tree[:, N]-K)
    else:
        option[:, N]=numpy.maximum(numpy.zeros(N+1), K-price_tree[:, N])

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

We need to call this new function (and the previous one as well but it's already been done)
{% highlight python %}
fast=binom_tree_call_put_faster(N=50, T=1, S0=100, sigma=0.2, r=0.06, K=99, call=False, array_out=False)
{% endhighlight %}

in order to time both functions as follows :

{% highlight python %}
%timeit binom_tree_call_put(N=50, T=1, S0=100, sigma=0.2, r=0.06, K=99, call=False, array_out=False)
{% endhighlight %}

gives the following average :
````
7.25 ms ± 118 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)
````

{% highlight python %}
%timeit binom_tree_call_put_faster(N=50, T=1, S0=100, sigma=0.2, r=0.06, K=99, call=False, array_out=False)
{% endhighlight %}

gives the following average :
````
86.6 µs ± 890 ns per loop (mean ± std. dev. of 7 runs, 10000 loops each)
````

In all of the testruns I did, the second function always ran around $$80$$ times faster than the first one.
