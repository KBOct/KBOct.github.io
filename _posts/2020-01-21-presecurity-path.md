---
title: "Pre Security"
date: 2020-01-21
categories: [cybersecurity]
tags: [cybersecurity]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Black Scholes pricing in Python"
#toc: true
#toc_label: "Contents"
#toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
#toc_sticky: true
mathjax: true
---


{% highlight python linenos %}

S_t = float(input("Stock price (in €): "))
sigma = float(input("Volatility (in %): "))
K = float(input("Strike price (in €): "))
ttm = float(input("Time to maturity (in days): "))

r = float(input("Interest rate (in %): "))
q = float(input("Dividend rate (in %): "))

sigma_sqrtttm = sqrt(Decimal(ttm)/365) * (sigma/100.0)
divdiscount = exp((-(q/100.0) * ttm)/365)
irdiscount = exp((-(r/100.0) * ttm)/365)

d1 = (log(S_t*divdiscount/K)+(r+.5*(sigma**2)) * ttm/365)/sigma_sqrtttm
d2 = d1 - sigma_sqrtttm

Nd1 = (1+erf(d1/sqrt(2)))/2
Nd2 = (1+erf(d2/sqrt(2)))/2
Nd1b = (1+erf(-d1/sqrt(2)))/2
Nd2b = (1+erf(-d2/sqrt(2)))/2

CallPrice = round(S_t*divdiscount*Nd1-K*irdiscount*Nd2, 2)
PutPrice = round(K*irdiscount*Nd2b-S_t*divdiscount*Nd1b, 2)

print(" ")
print("The Black-Scholes Call Price is " + str(CallPrice) + " €")
print("The Black-Scholes Put Price is " + str(PutPrice) + " €")
{% endhighlight %}

```

    Stock price (in €): 42.30
    Volatility (in %): 1
    Strike price (in €): 40.8
    Time to maturity (in days): 126
    Interest rate (in %): 0.41
    Dividend rate (in %): 7

    The Black-Scholes Call Price is 0.55 €
    The Black-Scholes Put Price is 0.0 €
