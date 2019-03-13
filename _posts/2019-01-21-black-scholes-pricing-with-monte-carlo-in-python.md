---
title: "Black Scholes pricing with Monte Carlo in Python"
date: 2019-01-21
categories: [quantitative finance, pricing, python]
tags: [quantitative finance, Black-Scholes, option pricing, Monte-Carlo, python]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Black Scholes pricing with Monte Carlo in Python"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---

{% highlight python linenos %}
import numpy as np
import math
import time

class OptionPricing:

	def __init__(self,S0,E,T,rf,sigma,iterations):

		self.S0 = S0
		self.E = E
		self.T = T
		self.rf = rf
		self.sigma = sigma
		self.iterations = iterations

	def call_option_simulation(self):

		option_data = np.zeros([self.iterations, 2])
		rand = np.random.normal(0,1, [1, self.iterations])
		stock_price = self.S0*np.exp(self.T*(self.rf - 0.5*self.sigma**2) + self.sigma * np.sqrt(self.T) * rand)
		option_data[:,1] = stock_price - self.E

		# average for the Monte Carlo Method
		average = np.sum(np.amax(option_data, axis = 1))/float(self.iterations)

		return np.exp(-1.0*self.rf*self.T) * average

	def put_option_simulation(self):

		option_data = np.zeros([self.iterations,2])
		rand = np.random.normal(0,1,[1,self.iterations])
		stock_price = self.S0*np.exp(self.T*(self.rf - 0.5*self.sigma**2) + self.sigma * np.sqrt(self.T) * rand)
		option_data[:,1] = self.E - stock_price

		# average for the Monte Carlo Method
		average = np.sum(np.amax(option_data, axis = 1))/float(self.iterations)

		return np.exp(-1.0*self.rf*self.T) * average

if __name__ == "__main__":

	S0=100					#underlying
	E=100					#strike
	T=1						#time to maturity
	rf=0.05					#risk-free interest rate
	sigma=0.2				#volatility of the underlying
	iterations=10000000		#number of Monte Carlo iterations

	model = OptionPricing(S0,E,T,rf,sigma,iterations)
	print("Call option price with Monte Carlo approach: ", model.call_option_simulation())
	print("Put option price with Monte Carlo approach: ", model.put_option_simulation())

{% endhighlight %}
