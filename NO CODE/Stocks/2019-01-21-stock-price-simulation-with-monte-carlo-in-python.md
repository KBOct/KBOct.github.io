---
title: "Stock price simulation with Monte Carlo in Python"
date: 2019-01-21
categories: [quantitative finance, prediction, python]
tags: [quantitative finance, stock price, stock prediction, simulation, Monte-Carlo, python]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Stock price simulation with Monte Carlo in Python"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---



{% highlight python linenos %}
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import style
from matplotlib.lines import Line2D
import pandas as pd
import quandl
import datetime


start_date = datetime.date(2017,1,3)
end_date = datetime.date.today()

quandl.ApiConfig.api_key = "5PgwbJYXkcVZgpEs9byv"


df = quandl.get('WIKI/MSFT.4', start_date=start_date, end_date=end_date, collapse="daily")
df = df.reset_index()
prices = df['Close']

{% endhighlight %}


{% highlight python linenos %}

returns = prices.pct_change()
pricelist = prices.tolist()
last_price = pricelist[-1]


num_simulations = 1000
num_days = 252

simulation_df=pd.DataFrame()

for x in range(num_simulations):
    count = 0
    daily_vol = returns.std()
    price_series = []
    price = last_price * (1 + np.random.normal(0, daily_vol))
    price_series.append(price)

    for y in range(num_days):
        if count == 251:
            break
        price = price_series[count] * (1 + np.random.normal(0, daily_vol))
        price_series.append(price)
        count += 1

    simulation_df[x] = price_series


<!-- # average for the Monte Carlo Method
#average = np.sum(np.amax(option_data, axis = 1))/float(self.iterations)
#return np.exp(-1.0*self.rf*self.T) * average -->

{% endhighlight %}
Then we plot

{% highlight python linenos %}

fig = plt.figure()
fig.suptitle('Monte Carlo stock closing price simulation: MSFT')
plt.plot(simulation_df)
plt.axhline(y = last_price, color = 'r', linestyle = '-.')
plt.xlabel('Day')
plt.ylabel('Price')
plt.show()

{% endhighlight %}

<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plot.ly/~KBOct/0.embed"></iframe>
