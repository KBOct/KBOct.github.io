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

<!-- # H1 Heading

## H2 Heading

### H3 Heading

Here's some basic text

And here's some *italic*

Here's some **bold** text

What about a [link](https://github.com/kboct)

Here's a bulleted list:
* First
+ Second
- Third


Here's a numbered list:
1. First
2. Second
3. Third -->

Python code block:
{% highlight python linenos %}
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import style
import pandas as pd
import quandl
import datetime
#style.use('ggplot')


start_date = datetime.date(2017,1,3)
end_date = datetime.date.today()

quandl.ApiConfig.api_key = "yourRegisteredQuandlAPIkey"


df = quandl.get('WIKI/AAP.4', start_date=start_date, end_date=end_date, collapse="daily")
df = df.reset_index()
prices = df['Close']

returns = prices.pct_change()
pricelist=prices.tolist()
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


fig = plt.figure()
fig.suptitle('Monte Carlo Simulation: MSFT')
plt.plot(simulation_df)
plt.axhline(y = last_price, color = 'r', linestyle = '-')
plt.xlabel('Day')
plt.ylabel('Price')
plt.show()


{% endhighlight %}

<!-- Here's some inline code `x+y`

Here's an image:
<img src="{{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/image1.jpg" alt="scilab numerical analysis plot" class="full">


Here's another image using Kramdown:
![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/image1.jpg)
{: .full}

Here's some math :

$$z=x+y$$

You can also put it inline $$z=x+y$$ -->
