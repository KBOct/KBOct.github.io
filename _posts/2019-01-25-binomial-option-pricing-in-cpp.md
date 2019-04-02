---
title: "Binomial option pricing in C++"
date: 2019-01-25
categories: [quantitative finance, pricing, cpp]
tags: [C++]
# tags: [quantitative finance, binomial tree, CRR, Cox Ross Rubinstein, options, pricing, C++, cpp]
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
#include <cmath>
#include <algorithm>
using namespace std;

double euro_call_pricing( const double& S,     // spot price
					    const double& X,     // exercice price
					    const double& r,     // interest rate (per period)
					    const double& u,     // up movement
					    const double& d){   // down movement
   double p_up = (exp(r)-d)/(u-d);
   double p_down = 1.0-p_up;
   double c_up = max(0.0,(u*S-X));
   double c_down = max(0.0,(d*S-X));
   double call_price = exp(-r)* (p_up*c_up+p_down*c_down);
   return call_price;
{% endhighlight %}

This one period setting is unrealistic, which is why I'll be coding the multiperiod binomial option pricing model. If we build a binary tree as such:

![alt]({{ site.url }}{{ site.baseurl }}/images/mutlibinomialtree.png)
{:class="img-responsive"}

Then the option's price is calculated iteratively by going from the "top" of the tree back to its root. For instance, in a simple 2-period model:

1) Calculate the derivative's payoff:

-	$$C_{up/up}=\left(C_{up/up}-K\right)_ + $$ for our first-case scenario
-	$$C_{down/up}=C_{up/down}=\left(C_{up/up}-K\right)_ +$$  for a recombining tree (meaning the downward move and the upward move are of equal intensity)
-	$$C_{down/down}=\left(C_{up/up}-K\right)_ +$$ for our last-case scenario

2) Calculate the 2 prices at period 1:

-	$$C_{up} = e^{-r}\left(q\ C_{up/up} + (1 - q)\ C_{up/down}\right)$$

-	$$C_{down} = e^{-r}\left(q\ C_{up/down} + (1 - q)\ C_{down/down}\right)$$

3) Value of the option:

$$C_0 = e^{-r}\left(q\ C_{up} + (1 - q)\ C_{down}\right)$$

Which looks like this in C++:

{% highlight c++ linenos %}
#include <cmath>
#include <algorithm>
#include <vector>

using namespace std;

double euro_call_pricing(const double& S, // spot
					const double& X,     // exercise price
					const double& r,     // IR
					const double& sigma, // vol
					const double& t,     // TTM
					const int& steps){  // number of steps in tree

	 double R = exp(r*(t/steps));            // stepwise IR
   double inv_R = 1.0/R;
   double u = exp(sigma*sqrt(t/steps));    // move up
   double u2 = u*u;
   double d = 1.0/u;
   double p_up = (R-d)/(u-d);
   double p_down = 1.0-p_up;

   vector<double> prices(steps+1);       // UL price
   prices[0] = S*pow(d, steps);

   for (int i=1; i<=steps; ++i) prices[i] = u2*prices[i-1];

   vector<double> call_values(steps+1);

   for (int i=0; i<=steps; ++i) call_values[i] = max(0.0, (prices[i]-X)); // call payoff

   for (int step=steps-1; step>=0; --step) {
      for (int i=0; i<=step; ++i) {
	 call_values[i] = (p_up*call_values[i+1]+p_down*call_values[i])* inv_R;
      };
   };

   return call_values[0];

};
{% endhighlight %}
