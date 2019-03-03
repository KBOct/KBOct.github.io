---
title: "Black Scholes pricing in Python 2"
date: 2019-01-21
categories: [quantitative finance, pricing, python 2]
tags: [quantitative finance, Black-Scholes, pricing, python 2]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Black Scholes pricing in Python 2"
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
from math import sqrt, exp, log, erf

from decimal import *
getcontext().prec = 5

S_t = float(input("Please type the stock price: "))
sigma = float(input("Please type the volatility: "))
K = float(input("Please type the strike price: "))
ttm = float(input("Please type the time to maturity in days: "))

r = float(input("Please type the interest rate: "))
q = float(input("Please type the dividend rate: "))

sigma_sqrtttm = sqrt(Decimal(ttm)/365) * sigma
divdiscount = exp((-q*ttm)/365)
irdiscount = exp((-r*ttm)/365)

d1 = (log(S_t*divdiscount/K)+(r+.5*(sigma**2)) * ttm/365)/sigma_sqrtttm
d2 = d1 - sigma_sqrtttm

Nd1 = (1+erf(d1/sqrt(2)))/2
Nd2 = (1+erf(d2/sqrt(2)))/2
Nd1b = (1+erf(-d1/sqrt(2)))/2
Nd2b = (1+erf(-d2/sqrt(2)))/2

CallPrice = round(S_t*divdiscount*Nd1-K*irdiscount*Nd2, 2)
PutPrice = round(K*irdiscount*Nd2b-S_t*divdiscount*Nd1b, 2)

print(" ")
print("The Black-Scholes Call Price is " + str(CallPrice))
print("The Black-Scholes Put Price is " + str(PutPrice))
{% endhighlight %}


<!-- Here's some inline code `x+y`

Here's an image:
<img src="{{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/image1.jpg" alt="scilab numerical analysis plot" class="full">


Here's another image using Kramdown:
![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/image1.jpg)
{: .full}

Here's some math :

$$z=x+y$$

You can also put it inline $$z=x+y$$ -->
