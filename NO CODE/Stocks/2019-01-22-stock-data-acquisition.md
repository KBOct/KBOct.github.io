---
title: "Stock Data Acquisition"
date: 2019-01-22
categories: [quantitative finance, data science, prediction, vba]
tags: [stock, data, live stock data, updated stock data, analysis, acquisition, excel, spreadsheet, vba]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Here, we'll explore different ways to acquire stock data in Excel VBA, Python 3 and R"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---
In this post I will present different ways in which one can acquire stock data to use in different frameworks and programming languages, essentially for resource allocation, portfolio optimization and rebalancing or to elaborate quantitative trading strategies.

# Yahoo!

## Data acquisition in VBA

Here we will get the PE Ratio, Forward PE, Beta, Market Cap and 52 Week High. A way to acquire such data in VBA would be to use this Subroutine :

{% highlight vb linenos %}
Sub qTest_3()

    Call clear_data

    Dim myrng As Range
    Dim lastrow As Long
    Dim row_count As Long
    Dim ws As Worksheet
    Set ws = Sheets("Main")

    col_count = 2
    row_count = 2

    'Find last row
    With ws
     lastrow = .Range("A" & .Rows.Count).End(xlUp).Row
    End With

    'set ticker range
    Set myrng = ws.Range(Cells(2, 1), Cells(lastrow, 1))

    'loop through tickers
    For Each ticker In myrng

        'Send web request
        Dim URL2 As String: URL2 = "https://finance.yahoo.com/quote/" & ticker & "/key-statistics?p=" & ticker
        Dim Http2 As New WinHttpRequest

        Http2.Open "GET", URL2, False
        Http2.Send

        Dim s As String
        'Get source code of site
        s = Http2.ResponseText

            Dim metrics As Variant
            '**** Metric fields here
            metrics = Array("trailingPE", "forwardPE", "beta", "marketCap", "fiftyTwoWeekHigh")


            'Split string here
            For Each element In metrics

                firstTerm = Chr(34) & element & Chr(34) & ":{" & Chr(34) & "raw" & Chr(34) & ":"
                secondTerm = "," & Chr(34) & "fmt" & Chr(34)

                nextPosition = 1

                On Error GoTo err_hdl

                Do Until nextPosition = 0
                    startPos = InStr(nextPosition, s, firstTerm, vbTextCompare)
                    stopPos = InStr(startPos, s, secondTerm, vbTextCompare)
                    split_string = Mid$(s, startPos + Len(firstTerm), stopPos - startPos - Len(secondTerm))
                    nextPosition = InStr(stopPos, s, firstTerm, vbTextCompare)

                    Exit Do
                Loop

                On Error GoTo 0


                Dim arr() As String
                arr = Split(split_string, ",")
                metric = arr(0)

                'Output to sheet
                ws.Range(Cells(row_count, col_count), Cells(row_count, col_count)).Value = metric
                col_count = col_count + 1

getData:

            Next element

            Dim symbol As String
            symbol = ticker

            col_count = 2
            row_count = row_count + 1

    Next ticker

    MsgBox ("Done")

    Exit Sub

err_hdl:
    ws.Range(Cells(row_count, col_count), Cells(row_count, col_count)).Value = "N/A"
    Resume getData

End Sub


Sub clear_data()

    Dim ws As Worksheet
    Set ws = Sheets("Main")
    Dim lastrow, lastcol As Long
    Dim myrng As Range

    With ws
     lastrow = .Range("A" & .Rows.Count).End(xlUp).Row
    End With

    lastcol = ws.Cells(1, Columns.Count).End(xlToLeft).Column

    Set myrng = ws.Range(Cells(2, 2), Cells(lastrow, lastcol))

    myrng.Clear

End Sub
{% endhighlight %}

## Data acquisition in Python



## Data acquisition in R


```R
# Get quantmod
if (!require("quantmod")) {
    install.packages("quantmod")
    library(quantmod)
}

start <- as.Date("2016-01-01")
end <- as.Date("2019-01-22")

# Here, we use quantmod function getSymbols and pass the desired ticker symbol AAPLa as
# string in first argument, then 'yahoo' and dates

getSymbols("AAPL", src = "yahoo", from = start, to = end)
```

'AAPL'


```R
# What class is AAPL?
class(AAPL)
```

<ol class=list-inline>
	<li>'xts'</li>
	<li>'zoo'</li>
</ol>



```R
# Let's see the first few rows
head(AAPL)
```


               AAPL.Open AAPL.High AAPL.Low AAPL.Close AAPL.Volume AAPL.Adjusted
    2016-01-04    102.61    105.37   102.00     105.35    67649400      99.49911
    2016-01-05    105.75    105.85   102.41     102.71    55791000      97.00573
    2016-01-06    100.56    102.37    99.87     100.70    68457400      95.10736
    2016-01-07     98.68    100.13    96.43      96.45    81094400      91.09340
    2016-01-08     98.55     99.11    96.76      96.96    70798000      91.57507
    2016-01-11     98.97     99.06    97.34      98.53    49739400      93.05787


getSymbols() created a global environment object called AAPL that is of the xts class which is an improved versions of the ts object for storing time series data. They allow multiple time series with the same time index to be stored in the same object.


### Visualizing Stock Data

Let's visualize the series with regular R plotting


```R
plot(AAPL[, "AAPL.Close"], main = "AAPL")
```


![alt]({{ site.url }}{{ site.baseurl }}/images/sda-yahoo-r.png)
{:class="img-responsive"}


# Google

## Morningstar

## Getting live stock data without code

# Quandl

## Data acquisition in Python

In this Python script we are going to retrieve Open, High, Low, Clos, Volume and the corresponding adjusted values as well as Ex-Dividend and Split Ratio

```python
import pandas as pd
import quandl
import datetime

# We will look at stock prices over the last 3 years, starting at January 1, 2016
start = datetime.datetime(2016,1,1)
end = datetime.date.today()

quandl.ApiConfig.api_key = "yourRegisteredQuandlAccountAPIkey"

# Let's get Apple stock data; Apple's ticker symbol is AAPL
# First argument is the series we want, second is the source ("yahoo" for Yahoo! Finance), third is the start date, fourth is the end date
s = "AAPL"
apple = quandl.get("WIKI/" + s, start_date=start, end_date=end)
```

```python
type(apple)
```
    pandas.core.frame.DataFrame

```python
apple.head()
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
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Volume</th>
      <th>Ex-Dividend</th>
      <th>Split Ratio</th>
      <th>Adj. Open</th>
      <th>Adj. High</th>
      <th>Adj. Low</th>
      <th>Adj. Close</th>
      <th>Adj. Volume</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-01-04</th>
      <td>102.61</td>
      <td>105.368</td>
      <td>102.00</td>
      <td>105.35</td>
      <td>67649387.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>99.136516</td>
      <td>101.801154</td>
      <td>98.547165</td>
      <td>101.783763</td>
      <td>67649387.0</td>
    </tr>
    <tr>
      <th>2016-01-05</th>
      <td>105.75</td>
      <td>105.850</td>
      <td>102.41</td>
      <td>102.71</td>
      <td>55790992.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>102.170223</td>
      <td>102.266838</td>
      <td>98.943286</td>
      <td>99.233131</td>
      <td>55790992.0</td>
    </tr>
    <tr>
      <th>2016-01-06</th>
      <td>100.56</td>
      <td>102.370</td>
      <td>99.87</td>
      <td>100.70</td>
      <td>68457388.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>97.155911</td>
      <td>98.904640</td>
      <td>96.489269</td>
      <td>97.291172</td>
      <td>68457388.0</td>
    </tr>
    <tr>
      <th>2016-01-07</th>
      <td>98.68</td>
      <td>100.130</td>
      <td>96.43</td>
      <td>96.45</td>
      <td>81094428.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>95.339552</td>
      <td>96.740467</td>
      <td>93.165717</td>
      <td>93.185040</td>
      <td>81094428.0</td>
    </tr>
    <tr>
      <th>2016-01-08</th>
      <td>98.55</td>
      <td>99.110</td>
      <td>96.76</td>
      <td>96.96</td>
      <td>70798016.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>95.213952</td>
      <td>95.754996</td>
      <td>93.484546</td>
      <td>93.677776</td>
      <td>70798016.0</td>
    </tr>
  </tbody>
</table>
</div>


```python
import matplotlib.pyplot as plt   # Import matplotlib
# This line is necessary for the plot to appear in a Jupyter notebook
%matplotlib inline
# Control the default size of figures in this Jupyter notebook
%pylab inline
pylab.rcParams['figure.figsize'] = (15, 9)   # Change the size of plots

apple["Adj. Close"].plot(grid = True) # Plot the adjusted closing price of AAPL
```
A little terminology :
- **Open** : price of the stock at the beginning of the trading day
- **High** : highest price of the stock on that trading day,
- **Low** : lowest price of the stock on that trading day, and
- **Close** : price of the stock at closing time.
- **Adjusted prices** : such as the adjusted close is the price of the stock that adjusts the price for corporate actions.
- **stock splits** : when the company makes each extant stock worth two and halves
- **dividends** : payout of company profits per share

Yahoo's API shutdown made market data sourcing a lot harder for quant traders and the values that are still available are no longer consistent with other sources.


### Visualizing Stock Data

    Populating the interactive namespace from numpy and matplotlib


    <matplotlib.axes._ subplots.AxesSubplot at 0x1115fda0>

![alt]({{ site.url }}{{ site.baseurl }}/images/sda-yahoo-python.png)
{:class="img-responsive"}

## Data acquisition in R

```R

if (!require("Quandl")) {
    install.packages("Quandl")
}


Quandl.api_key("yourRegisteredQuandlAccountAPIkey")
AAPL <- Quandl(c("MF1", "WIKI/AAPL.4"),
                    type = "xts",
                    collapse = "daily",  
                    start_date="2010-01-01",
                    end_date="2019-01-22")

```

**Remark** : Quandl could be used to acquire data from Yahoo and Google sources but it's not recommended since Yahoo's shutdown and Morningstar library's recent issues
