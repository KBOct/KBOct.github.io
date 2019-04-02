---
title: "Pricing with random numbers in C++"
date: 2019-01-25
categories: [quantitative finance, pricing, cpp]
tags: [C++]
# tags: [quantitative finance, random numbers, simulation, pricing, C++, cpp]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Pricing with random numbers in C++"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---



{% highlight c++ linenos %}
#include <cmath>
using namespace std;
#include "normdist.h"

double logn_rv_simulation(const double& S,
					  const double& r,  // IR
					  const double& sigma,  // vol
					  const double& T_t) {  // TTM
   double R = (r - 0.5 * pow(sigma,2) ) * T_t;
   double SD = sigma * sqrt(T_t);
   return S * exp(R + SD * random_normal());
};
{% endhighlight %}



{% highlight c++ linenos %}
#include <cmath>  
#include <algorithm>
using namespace std;
#include "normdist.h"

double euro_put_pricing_simu(const double& S,
					   const double& X,
					   const double& r,
					   const double& sigma,
					   const double& T_t,
					   const int& no_sims) {
    double sigma_sqr = sigma * sigma;
    double R = (r - 0.5 * sigma_sqr)* T_t;
    double SD = sigma * sqrt(T_t);
    double sum_payoffs = 0.0;
    for (int n=1; n<=no_sims; n++) {
	double S_T = S * exp(R + SD * random_normal());
	sum_payoffs += max(0.0, X-S_T);
    };
    return exp(-r*T_t) * (sum_payoffs/double(no_sims));
};

{% endhighlight %}



{% highlight c++ linenos %}
#include <cmath>  
#include <algorithm>
using namespace std;
#include "normdist.h"

double euro_put_pricing_simu(const double& S,
					   const double& X,
					   const double& r,
					   const double& sigma,
					   const double& T_t,
					   const int& no_sims) {
    double sigma_sqr = sigma * sigma;
    double R = (r - 0.5 * sigma_sqr)* T_t;
    double SD = sigma * sqrt(T_t);
    double sum_payoffs = 0.0;
    for (int n=1; n<=no_sims; n++) {
	double S_T = S * exp(R + SD * random_normal());
	sum_payoffs += max(0.0, X-S_T);
    };
    return exp(-r*T_t) * (sum_payoffs/double(no_sims));
};

{% endhighlight %}
