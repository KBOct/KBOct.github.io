---
title: "Monte Carlo simulation for path-dependent options pricing in C++"
date: 2019-01-25
categories: [quantitative finance, pricing, cpp]
tags: [C++]
# tags: [quantitative finance, Monte Carlo, simulation, path-dependent, option pricing, C++, cpp]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Monte Carlo simulation for path-dependent options pricing in C++"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---

<!-- Monte Carlo simulation can be used to price a lot of different options. The limitation is that the options should be European. American options can not be priced by simulation methods. In chapter 11 we looked at a general simulation case where we wrote a generic routine which we passed a payoff function to, and the payoff function was all that was necessary to dene an option value. The payoff function in that case was a function of the terminal price of the underlying security. The only difference to the previous case is that we now have to generate a price sequence and write the terminal payoff of the derivative in terms of that,
instead of just generating the terminal value of the underlying security from the lognormal assumption. -->

We'll use lognormal random variables of the type:
$$ S_T = S_t e^{\left( r-\frac{1}{2} \sigma^2 \right) \left( T-t\right)+\sigma \sqrt{T-t}X}$$
where $$T-t$$ is time to maturity. With &&\Delta t$$ the step of the time discretization between $$0$$ and $$T-t$$

The general term of the price sequence is:
$$ S_t+\Delta t = S_t e^{\left( r-\frac{1}{2} \sigma^2 \right) \Delta t +\sigma\sqrt{\Delta t}X}$$

{% highlight c++ linenos %}
#include <cmath>
#include <vector>
using namespace std;
#include "normdist.h"

vector<double>
simul_lognorm_RV_sequence(const double& S,  // current value of underlying
					  const double& r,  // interests
					  const double& sigma,  // volatility
					  const double& time,  // time to maturity
					  const int& no_steps){  // discretization size
    vector<double> prices(no_steps);
    double delta_t = time/no_steps;
    double R = (r-0.5*pow(sigma,2)) * delta_t;
    double SD = sigma * sqrt(delta_t);
    double S_t = S;                       // initialize at current price
    for (int i=0;  i<no_steps; ++i) {   
	S_t = S_t * exp(R + SD * random_normal());
	prices[i]=S_t;
    };
    return prices;
};

{% endhighlight %}

{% highlight c++ linenos %}
#include <cmath>
#include <numeric>
#include <vector>
using namespace std;

double payoff_arithmetric_average_call(const vector<double>& prices, const double& X) {
    double sum=accumulate(prices.begin(), prices.end(),0.0);
    double avg = sum/prices.size();
    return max(0.0,avg-X);
};

double payoff_geometric_average_call(const vector<double>& prices, const double& X) {
    double logsum=log(prices[0]);
    for (unsigned i=1;i<prices.size();++i){ logsum+=log(prices[i]); };
    double avg = exp(logsum/prices.size());
    return max(0.0,avg-X);
};
{% endhighlight %}

{% highlight c++ linenos %}
#include <cmath>
using namespace std;
#include "fin_recipes.h"

double
derivative_price_simulate_european_option_generic(const double& S, // price of underlying
						  const double& X, // used by user provided payoff function
						  const double& r, // risk free interest rate
						  const double& sigma, // volatility
						  const double& time, // time to maturity
						  double payoff(const vector<double>& prices,
								const double& X),
						  // user provided function
						  const int& no_steps, // number of steps in generated price sequence
						  const int& no_sims) { // number of simulations to run
    double sum_payoffs=0;
    for (int n=0; n<no_sims; n++) {
	vector<double>prices = simul_lognorm_RV_sequence(S,r,sigma,time,no_steps);
	sum_payoffs += payoff(prices,X);
    };
    return exp(-r*time) * (sum_payoffs/no_sims);
};
{% endhighlight %}
