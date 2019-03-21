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

double simulate_lognormal_random_variable(const double& S,  // current value of variable
					  const double& r,  // interest rate
					  const double& sigma,  // volatitily
					  const double& time) {  // time to final date
   double R = (r - 0.5 * pow(sigma,2) ) * time;
   double SD = sigma * sqrt(time);
   return S * exp(R + SD * random_normal());
};
{% endhighlight %}


C++ code block:
{% highlight c++ linenos %}
#include <cmath>  
#include <algorithm>     // define the max() function
using namespace std;
#include "normdist.h"   // definition of random number generator

double option_price_put_european_simulated(const double& S,
					   const double& X,
					   const double& r,
					   const double& sigma,
					   const double& time,
					   const int& no_sims) {
    double sigma_sqr = sigma * sigma;
    double R = (r - 0.5 * sigma_sqr)* time;
    double SD = sigma * sqrt(time);
    double sum_payoffs = 0.0;
    for (int n=1; n<=no_sims; n++) {
	double S_T = S * exp(R + SD * random_normal());
	sum_payoffs += max(0.0, X-S_T);
    };
    return exp(-r*time) * (sum_payoffs/double(no_sims));
};

{% endhighlight %}


C++ code block:
{% highlight c++ linenos %}
#include <cmath>  
#include <algorithm>     // define the max() function
using namespace std;
#include "normdist.h"   // definition of random number generator

double option_price_put_european_simulated(const double& S,
					   const double& X,
					   const double& r,
					   const double& sigma,
					   const double& time,
					   const int& no_sims) {
    double sigma_sqr = sigma * sigma;
    double R = (r - 0.5 * sigma_sqr)* time;
    double SD = sigma * sqrt(time);
    double sum_payoffs = 0.0;
    for (int n=1; n<=no_sims; n++) {
	double S_T = S * exp(R + SD * random_normal());
	sum_payoffs += max(0.0, X-S_T);
    };
    return exp(-r*time) * (sum_payoffs/double(no_sims));
};

{% endhighlight %}
