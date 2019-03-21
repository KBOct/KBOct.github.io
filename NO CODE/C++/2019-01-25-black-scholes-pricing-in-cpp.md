---
title: "Black Scholes pricing in C++"
date: 2019-01-25
categories: [quantitative finance, pricing, cpp]
tags: [C++]
# tags: [quantitative finance, Black-Scholes, option pricing, C++, cpp]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Black Scholes pricing in C++"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---
BS Call:
{% highlight c++ linenos %}
#include <cmath>              // mathematical C library
#include "normdist.h"         // the calculation of the cumularive normal distribution

double option_price_call_black_scholes(const double& S,       // spot (underlying) price
				       const double& K,       // strike (exercise) price,
				       const double& r,       // interest rate
				       const double& sigma,   // volatility
				       const double& time) {  // time to maturity
    double time_sqrt = sqrt(time);
    double d1 = (log(S/K)+r*time)/(sigma*time_sqrt)+0.5*sigma*time_sqrt;
    double d2 = d1-(sigma*time_sqrt);
    double c  = S*N(d1) - K*exp(-r*time) * N(d2);
    return c;
};

{% endhighlight %}

BS Put:
{% highlight c++ linenos %}
#include <cmath>              // mathematical library
#include "normdist.h"          // this defines the normal distribution
using namespace std;

double option_price_put_black_scholes( const double& S,      // spot price
				       const double& K,      // Strike (exercise) price,
				       const double& r,      // interest rate
				       const double& sigma,  // volatility
				       const double& time) {  
    double time_sqrt = sqrt(time);
    double d1 = (log(S/K)+r*time)/(sigma*time_sqrt) + 0.5*sigma*time_sqrt;
    double d2 = d1-(sigma*time_sqrt);
    double p =  K * exp(-r*time) * N(-d2) - S * N(-d1);
    return p;
};

{% endhighlight %}

BS on dividend paying Call option:
{% highlight c++ linenos %}
#include <cmath>              // mathematical library
#include <vector>
#include "fin_recipes.h"          // define the black scholes price

double option_price_european_call_dividends( const double& S,               
					     const double& K,
					     const double& r,
					     const double& sigma,           
					     const double& time_to_maturity,
					     const vector<double>& dividend_times,
					     const vector<double>& dividend_amounts ) {  
    double adjusted_S = S;
    for (int i=0;i<dividend_times.size();i++) {
	if (dividend_times[i]<=time_to_maturity){
	    adjusted_S -= dividend_amounts[i] * exp(-r*dividend_times[i]);
	};
    };
    return option_price_call_black_scholes(adjusted_S,K,r,sigma,time_to_maturity);
};
{% endhighlight %}
