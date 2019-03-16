---
title: "Binomial option pricing in C++"
date: 2019-01-25
categories: [quantitative finance, pricing, cpp]
tags: [quantitative finance, binomial tree, CRR, Cox Ross Rubinstein, options, pricing, C++, cpp]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Binomial option pricing in C++"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---

The one period model can be implemented like this:

{% highlight c++ linenos %}
#include <cmath>             // standard mathematical library
#include <algorithm>             // defining the max() operator
using namespace std;

double option_price_call_european_binomial( const double& S,     // spot price
					    const double& X,     // exercice price
					    const double& r,     // interest rate (per period)
					    const double& u,     // up movement
					    const double& d){   // down movement
   double p_up = (exp(r)-d)/(u-d);
   double p_down = 1.0-p_up;
   double c_u = max(0.0,(u*S-X));
   double c_d = max(0.0,(d*S-X));
   double call_price = exp(-r)* (p_up*c_u+p_down*c_d);
   return call_price;
{% endhighlight %}

This one period setting is unrealistic, which is why I'll be coding the multiperiod binomial option pricing model. If we build a binary tree as such:


Then the option's price is calculated iteratively by going from the 'top' of the tree back to its root:

1.

2.

3. Value of the option: $$C_0 = e^-r(q C_h + (1 - q) C_b)$$

Which looks like this in C++:

{% highlight c++ linenos %}
#include <cmath>             // standard mathematical library
#include <algorithm>             // defining the max() operator
#include <vector>           // STL vector templates
using namespace std;

double option_price_call_european_binomial( const double& S,     // spot price
					    const double& X,     // exercice price
					    const double& r,     // interest rate
					    const double& sigma, // volatility
					    const double& t,     // time to maturity
					    const int& steps){  // no steps in binomial tree
   double R = exp(r*(t/steps));            // interest rate for each step
   double Rinv = 1.0/R;                    // inverse of interest rate
   double u = exp(sigma*sqrt(t/steps));    // up movement
   double uu = u*u;
   double d = 1.0/u;
   double p_up = (R-d)/(u-d);
   double p_down = 1.0-p_up;
   vector<double> prices(steps+1);       // price of underlying
   prices[0] = S*pow(d, steps);  // fill in the endnodes.
   for (int i=1; i<=steps; ++i) prices[i] = uu*prices[i-1];
   vector<double> call_values(steps+1);       // value of corresponding call
   for (int i=0; i<=steps; ++i) call_values[i] = max(0.0, (prices[i]-X)); // call payoffs at maturity
   for (int step=steps-1; step>=0; --step) {
      for (int i=0; i<=step; ++i) {
	 call_values[i] = (p_up*call_values[i+1]+p_down*call_values[i])* Rinv;
      };
   };
   return call_values[0];
};
{% endhighlight %}
