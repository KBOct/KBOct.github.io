---
title: "Advanced option pricing via Heston in Matlab"
date: 2019-01-19
categories: [quantitative finance, pricing, matlab]
tags: [quantitative finance, Heston, option pricing, matlab]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Advanced option pricing via Heston in Matlab"
mathjax: true
author_profile: false
---

# Heston model closed-form formula

In the Heston model, contingent claims can be priced through a semi-closed form formula.
$$C_0 = S_0 P_1 - e^{-rT} K P_2 $$
where P_1 and P_2 are 2 probability-related quantities. However, these probabilities can't be calculated in a straightforward manner because their transition densities don't have a closed form in the Heston model. Which leads us to characteristic functions


# Heston model's characteristic function


{% highlight matlab linenos %}
function y = chfun_heston(s0, v0, vbar, a, vvol, r, rho, t, w);
% Heston characteristic function.
% Inputs:
% s0: stock price
% v0: initial volatility (v0^2 initial variance)
% vbar: long-term variance mean
% a: variance mean-reversion speed
% vvol: volatility of the variance process
% r : risk-free rate
% rho: correlation between the Weiner processes for the stock price and its variance
% w: points at which to evaluate the function
% Output:

% Characteristic function of log (St) in the Heston model

% Interim calculations
alpha = -w.* w/2 - i*w/2;
beta = a - rho*vvol*i*w;
gamma = vvol*vvol/2;
h = sqrt(beta.* beta - 4*alpha*gamma);
rplus = (beta + h)/vvol/vvol;
rminus = (beta - h)/vvol/vvol;
g=rminus./rplus;

% Required inputs for the characteristic function
C = a * (rminus * t - (2 / vvol^2) .* log((1 - g .* exp(-h*t))./(1-g)));
D = rminus .* (1 - exp(-h * t))./(1 - g .* exp(-h*t));

% Characteristic function evaluated at points w
y = exp(C*vbar + D*v0 + i*w*log(s0*exp(r*t)));
{% endhighlight %}


# Heston Call pricing function

{% highlight matlab linenos %}
function y = call_heston_cf(s0, v0, vbar, a, vvol, r, rho, t, k)
% Heston call value using characteristic functions.
% y = call_heston_cf(s0, v0, vbar, a, vvol, r, rho, t, k)

% Inputs:
% s0: stock price
% v0: initial volatility (v0^2 initial variance)
% vbar: long-term variance mean
% a: variance mean-reversion speed
% vvol: volatility of the variance process
% r: risk-free rate
% rho: correlation between the Weiner processes of the stock price and its variance
% t: time to maturity
% k: option strike
% chfun_heston: Heston characteristic function


% 1st step: calculate pi1 and pi2

% Inner integral 1
int1 = @(w, s0, v0, vbar, a, vvol, r, rho, t, k) real(exp(-i.* w * log(k)).* chfun_heston(s0, v0, vbar, a, vvol, r, rho, t, w-i)./(i*w.* chfun_heston(s0, v0, vbar, a, vvol, r, rho, t, -i))); % inner integral1
int1 = integral(@(w)int1(w,s0, v0, vbar, a, vvol, r, rho, t, k),0,100); % numerical integration
pi1 = int1/pi+0.5; % final pi1

% Inner integral 2:
int2 = @(w, s0, v0, vbar, a, vvol, r, rho, t, k) real(exp(-i.* w * log(k)).* chfun_heston(s0, v0, vbar, a, vvol, r, rho, t, w)./(i*w));
int2 = integral(@(w)int2(w,s0, v0, vbar, a, vvol, r, rho, t, k),0,100);int2 = real(int2);
pi2 = int2/pi+0.5; % final pi

% 2rd step: calculate call value
y = s0*pi1-exp(-r*t) * k * pi2; 
end

%Numerical Example 2: Call valuation in the Heston model.

% function y = call_heston_cf(s0, v0, vbar, a, vvol, r, rho, t);
%>> call_heston_cf(1, 0.16, 0.16, 1, 2, 0, -0.8, 10, 2)
%ans = 0.0495
{% endhighlight %}
