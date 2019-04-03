---
title: "Stocks and Machine Learning"
date: 2019-01-22
categories: [quantitative finance, data science, prediction, python]
tags: [python]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Stocks and Machine Learning"
#toc: true
#toc_label: "Contents"
#toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
#toc_sticky: true
mathjax: true
---



```python
import os
import numpy as np
import pandas as pd
from sklearn.svm import SVR
import matplotlib.pyplot as plt

# Use here the path containing the data file
path_data = './'
filename = 'aapl.csv'
```

```python
data = pd.read_csv(os.path.join(path_data, filename), sep=',')
type(data)
```

    pandas.core.frame.DataFrame


```python
data.head(n=5)
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Volume</th>
      <th>Adj Close</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008-10-14</td>
      <td>116.26</td>
      <td>116.40</td>
      <td>103.14</td>
      <td>104.08</td>
      <td>70749800</td>
      <td>104.08</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2008-10-13</td>
      <td>104.55</td>
      <td>110.53</td>
      <td>101.02</td>
      <td>110.26</td>
      <td>54967000</td>
      <td>110.26</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2008-10-10</td>
      <td>85.70</td>
      <td>100.00</td>
      <td>85.00</td>
      <td>96.80</td>
      <td>79260700</td>
      <td>96.80</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2008-10-09</td>
      <td>93.35</td>
      <td>95.80</td>
      <td>86.60</td>
      <td>88.74</td>
      <td>57763700</td>
      <td>88.74</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2008-10-08</td>
      <td>85.91</td>
      <td>96.33</td>
      <td>85.68</td>
      <td>89.79</td>
      <td>78847900</td>
      <td>89.79</td>
    </tr>
  </tbody>
</table>
</div>




```python
dates_pandas=data['Date']
type(dates_pandas)
```

    pandas.core.series.Series


```python
dates=data['Date'].values
#dates=dates_pandas.values
```


```python
type(dates)
len(dates)
```

    6081


```python
dates = np.reshape(dates,(len(dates), 1))
```


```python
type(dates)
```

    numpy.ndarray


```python
data['Date'].head(n=20).values
```


    array(['2008-10-14', '2008-10-13', '2008-10-10', '2008-10-09',
           '2008-10-08', '2008-10-07', '2008-10-06', '2008-10-03',
           '2008-10-02', '2008-10-01', '2008-09-30', '2008-09-29',
           '2008-09-26', '2008-09-25', '2008-09-24', '2008-09-23',
           '2008-09-22', '2008-09-19', '2008-09-18', '2008-09-17'],
          dtype=object)


```python
for row in data['Open'].head(n=20).values:
    print(float(row))
```

    116.26
    104.55
    85.7
    93.35
    85.91
    100.48
    91.96
    104.0
    108.01
    111.92
    108.25
    119.62
    124.91
    129.8
    127.27
    131.85
    139.94
    142.6
    130.57
    138.49



```python
dates=[]
prices=[]

def get_data(filename):

    data = pd.read_csv(os.path.join(path_data, filename), sep=',')
    #dates=data['Date'].tolist()
    #prices=data['Open'].tolist()
    for row in data['Date'].head(n=200).values:
        dates.append(int(row[-2:]))

    for row in data['Open'].head(n=200).values:
        prices.append(float(row))

    return
```


```python
#import numba

#@numba.jit

def predict_price(dates, prices, x):
    dates = np.reshape(dates,(len(dates), 1)) # converting to matrix of n X 1

    svr_lin = SVR(kernel= 'linear', C= 1e3)
    svr_poly = SVR(kernel= 'poly', C= 1e3, degree= 2)
    svr_rbf = SVR(kernel= 'rbf', C= 1e3, gamma= 0.1) # defining the support vector regression models
    svr_rbf.fit(dates, prices) # fitting the data points in the models
    svr_lin.fit(dates, prices)
    svr_poly.fit(dates, prices)

    plt.scatter(dates, prices, color= 'black', label= 'Data') # plotting the initial datapoints
    plt.plot(dates, svr_rbf.predict(dates), color= 'red', label= 'RBF model') # plotting the line made by the RBF kernel
    plt.plot(dates,svr_lin.predict(dates), color= 'green', label= 'Linear model') # plotting the line made by linear kernel
    plt.plot(dates,svr_poly.predict(dates), color= 'blue', label= 'Polynomial model') # plotting the line made by polynomial kernel
    plt.xlabel('Date')
    plt.ylabel('Price')
    plt.title('Support Vector Regression')
    plt.legend()
    plt.show()

    return svr_rbf.predict(x)[0], svr_lin.predict(x)[0], svr_poly.predict(x)[0]
```


```python
get_data('aapl.csv')
```


```python
predicted_price = predict_price(dates, prices, 29)
```


![alt]({{ site.url }}{{ site.baseurl }}/images/stockpred/output_13_0.png)
{:class="img-responsive"}


```python
print(predicted_price)
```

    (160.81396310582937, 160.40999999904983, 159.21641205627645)
