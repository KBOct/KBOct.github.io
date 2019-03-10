---
title: "Black Scholes pricing with Greeks in Python 2"
date: 2019-01-21
categories: [quantitative finance, pricing, python 2]
tags: [quantitative finance, Black-Scholes, option pricing, greeks, python 2]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Black Scholes pricing with Greeks in Python 2"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---

<!-- # H1 Heading

## H2 Heading

### H3 Heading

Here's some basic text

And here's some *italic*

Here's some **bold** text

What about a [link](https://github.com/kboct)

Here's a bulleted list:
* First
+ Second
- Third


Here's a numbered list:
1. First
2. Second
3. Third -->

Python code block:
{% highlight python linenos %}
"""
# The Black Scholes Formula for a European Put
# CallPutFlag - This is set to 'c' for call option, anything else for put
# S - Stock price
# K - Strike price
# T - Time to maturity
# r - Riskfree interest rate
# d - Dividend yield
# v - Volatility
"""
import matplotlib.animation as animation
import matplotlib.pyplot as plt
import numpy as np
from scipy.stats import norm
from math import *

def BlackScholes(CallPutFlag,S,K,T,r,d,v):
    d1 = (log(float(S)/K)+((r-d)+v*v/2.) * T)/(v*sqrt(T))
    d2 = d1-v*sqrt(T)
    if CallPutFlag=='c':
        return S*exp(-d*T) * norm.cdf(d1)-K*exp(-r*T) * norm.cdf(d2)
    else:
        return K*exp(-r*T) * norm.cdf(-d2)-S*exp(-d * T) * norm.cdf(-d1)

plt.clf()
fig,ax = plt.subplots()

maturity = 0

S = np.linspace(80,120,200)

p = []
for i in S:
    p.append(BlackScholes('p', i, 100, 0.005, 0.06, 0, 0.4))

line, = ax.plot(p)

def update(step):
    p = []
    for i in S:
        p.append(BlackScholes('p', i, 100, step, 0.06, 0, 0.4))
    line.set_ydata(p)

def data_gen():
    expStop = 0.0005
    expStart = 1.5
    T = np.linspace(expStop,expStart,200)
    m = -log(expStop/expStart)/expStart
    for t in T:
        yield expStart*exp(-m*t)

ani = animation.FuncAnimation(fig, update, data_gen, interval=100)

plt.show()
{% endhighlight %}

We get the following animation :

![alt]({{ site.url }}{{ site.baseurl }}/images/CallBS.gif)
{:class="img-responsive"}

Then we go ahead and compute the greeks according to the following formulas:

$$\delta= \frac{\partial P}{\partial S}$$

$$\delta_{Call}= N(d_{1})$$

$$\delta_{Put} = \delta_{Call}-1= N(d_{1})-1$$


{% highlight python linenos %}
#The Call Greeks
"""Calculating the partial derivatives for a Black Scholes Option (Call)
# S - Stock price
# K - Strike price
# T - Time to maturity
# r - Risk-free interest rate
# q - Dividend yield
# sigma - Volatility
Return:
Delta: partial derivative with respect to  S
Gamma: second partial derivative with respect to  S
Theta: partial derivative with respect to  T
Vega: partial derivative with respect to  sigma
Rho: partial derivative with respect to  r
"""
from scipy.stats import norm
from math import *

def Black_Scholes_Greeks_Call(S, K, r, sigma, T, q):
    T_sqrt = sqrt(T)
    d1 = (log(float(S)/K)+((r-q)+sigma*sigma/2.) * T)/(sigma*T_sqrt)
    d2 = d1-sigma*T_sqrt
    Delta = norm.cdf(d1)
    Gamma = norm.pdf(d1)/(S*sigma*T_sqrt)
    Theta =- (S*sigma*norm.pdf(d1))/(2*T_sqrt) - r*K*exp(-r*T) * norm.cdf(d2)
    Vega = S * T_sqrt*norm.pdf(d1)
    Rho = K*T*exp(-r*T) * norm.cdf(d2)

    return Delta, Gamma, Theta, Vega, Rho

print(Black_Scholes_Greeks_Call(100, 100, 0.005, 0.06, 0.4, 0))
{% endhighlight %}

```
(0.5285710345530259, 0.10486079971293322, -2.1437085315040076, 25.166591931103973, 20.49713093369678)
```
With both the underlying price equal to the strike price, the call option is at the money and we get a Delta close to $$\frac{1}{2}$$ (slightly higher because the option is at $$0.4 \times 1 \text{ year} = \text{4 months and 24 days}$$ to expiry) which is what we should expect


With a put option we get :
{% highlight python linenos %}
#The Put Greeks
"""Calculating the partial derivatives for a Black Scholes Option (Put)
# S - Stock price
# K - Strike price
# T - Time to maturity
# r - Riskfree interest rate
# d - Dividend yield
# v - Volatility
Return:
Delta: partial wrt S
Gamma: second partial wrt S
Theta: partial wrt T
Vega: partial wrt v
Rho: partial wrt r
"""

from scipy.stats import norm
from math import *

def Black_Scholes_Greeks_Put(S, K, r, v, T, d):
    """Calculate partial derivatives for a Black Scholes Option (Put)
    """
    T_sqrt = sqrt(T)
    d1 = (log(float(S)/K)+r*T)/(v*T_sqrt) + 0.5*v*T_sqrt
    d2 = d1-(v*T_sqrt)
    Delta = -norm.cdf(-d1)
    Gamma = norm.pdf(d1)/(S*v*T_sqrt)
    Theta = -(S*v*norm.pdf(d1)) / (2*T_sqrt)+ r*K * exp(-r*T) * norm.cdf(-d2)
    Vega = S * T_sqrt * norm.pdf(d1)
    Rho = -K*T*exp(-r*T) * norm.cdf(-d2)

    return Delta, Gamma, Theta, Vega, Rho


print(Black_Scholes_Greeks_Put(100, 100, 0.005, 0.06, 0.4, 0))

{% endhighlight %}
