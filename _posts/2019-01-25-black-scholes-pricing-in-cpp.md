---
title: "Black Scholes pricing in C++"
date: 2019-01-25
categories: [quantitative finance, pricing, cpp]
tags: [C++]
# tags: [quantitative finance, Black-Scholes, option pricing, C++, cpp]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Black Scholes pricing in C++"
#toc: true
#toc_label: "Contents"
#toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
#toc_sticky: true
mathjax: true
---
Black-Scholes Call:
{% highlight c++ linenos %}
#include <cmath>
#include "normdist.h"

using namespace std;

double euro_call_BS_pricing(const double& S,   // UL price
				       const double& K,       // strike
				       const double& r,       // IR
				       const double& sigma,   // vol
				       const double& T) {  // time to maturity
    double T_sqrt = sqrt(T);
    double d1 = (log(S/K)+r*T)/(sigma*T_sqrt)+0.5*sigma*T_sqrt;
    double d2 = d1-(sigma*T_sqrt);
    double c  = S*N(d1) - K*exp(-r*T) * N(d2);
    return c;
};

{% endhighlight %}

Black-Scholes Put pricing:
{% highlight c++ linenos %}
double euro_put_BS_pricing( const double& S,      // UL price
				       const double& K,      // strike
				       const double& r,      // IR
				       const double& sigma,  // vol
				       const double& T) {  
    double T_sqrt = sqrt(T);
    double d1 = (log(S/K)+r*T)/(sigma*T_sqrt) + 0.5*sigma*T_sqrt;
    double d2 = d1-(sigma*T_sqrt);
    double p =  K * exp(-r*T) * N(-d2) - S * N(-d1);
    return p;
};

{% endhighlight %}

Black-Scholes pricing on dividend paying Call option:

{% highlight c++ linenos %}
double euro_call_BS_dividend_pricing( const double& S,               
					     const double& K,
					     const double& r,
					     const double& sigma,           
					     const double& T,
					     const vector<double>& dividend_periods,
					     const vector<double>& dividend_amounts ) {  
    double adjusted_S = S;
    for (int i=0;i<dividend_periods.size();i++) {
	if (dividend_periods[i]<=T){
	    adjusted_S -= dividend_amounts[i] * exp(-r*dividend_periods[i]);
	};
    };
    return euro_call_BS_pricing(adjusted_S,K,r,sigma,T);
};
{% endhighlight %}
