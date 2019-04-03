---
title: "Bonds in C++"
date: 2019-01-25
categories: [quantitative finance, pricing, bonds, cpp]
tags: [C++]
# tags: [quantitative finance, bonds, t-bills, t-notes, treasury, yield, duration, C++, cpp]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Bonds in C++"
#toc: true
#toc_label: "Contents"
#toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
#toc_sticky: true
mathjax: true
---
<!-- The price of a bond is the present value of its future cash ows. If we consider a coupon bond like a US goverment bond (T-Bond), the cash flows look like
t = 0 1 2 3    T
Coupon C C C    C
Face value F

The current price of the bond is:
P0 = XT
t=1
C
(1 + r)t + F
(1 + r)T

with discrete compounding, and
P0 =
XT
t=1
e􀀀rtC + e􀀀rTF

with continous compounding. The interest rate r is fixed, which means that the term structure is flat. -->



Bonds price:

{% highlight c++ linenos %}
#include <cmath>
#include <vector>
using namespace std;

double bond_pricing(const vector<double>& CF_periods,
		   const vector<double>& CF,
		   const double& r) {
    double p=0;
    for (int i=0;i<CF_periods.size();i++) {
	p += exp(-r*CF_periods[i])* CF[i];
    };
    return p;
};

{% endhighlight %}

Bonds yield to maturity:

{% highlight c++ linenos %}
#include "bond_pricing.h"
using namespace std;

double bonds_yield_to_maturity( const vector<double>& CF_periods,
				const vector<double>& CF_amounts,
				const double& bondprice) {
  const float ACCURACY = 1e-5;
  const int MAX_ITER = 200;
  double bot=0, top=1.0;
  while (bond_pricing(CF_periods, CF_amounts, top) > bondprice) {
    top = top*2;
  };
  double r = 0.5 * (top+bot);
  for (int i=0;i<MAX_ITER;i++){
    double diff = bond_pricing(CF_periods, CF_amounts,r) - bondprice;
    if (std::fabs(diff)<ACCURACY) return r;
    if (diff>0.0)  { bot=r;}
    else           { top=r; };
    r = 0.5 * (top+bot);
  };
  return r;
};

{% endhighlight %}

Bonds regular duration:

{% highlight c++ linenos %}
double bond_reg_duration(const vector<double>& CF_periods,
		      const vector<double>& CF,
		      const double& r) {
    double S=0;
    double D1=0;
    for (int i=0;i<CF_periods.size();i++){
	S  +=                     CF[i] * exp(-r*CF_periods[i]);
 	D1 += CF_periods[i] * CF[i] * exp(-r*CF_periods[i]);
    };
    return D1 / S;
};

{% endhighlight %}
