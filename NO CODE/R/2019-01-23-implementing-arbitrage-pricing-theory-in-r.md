---
title: "Implementing Arbitrage Pricing Theory in R"
date: 2019-01-23
categories: [quantitative finance, pricing, factor models, r]
tags: [arbitrage pricing theory, APT, Arbitrage Pricing Theory, model implementation, asset pricing, R, statistics]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Implementing Arbitrage Pricing Theory in R"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---
# Arbitrage pricing theory

The implementation of APT can be split into four steps: identifying the factors,
estimating the factor coefficient, estimating the factor premiums, and pricing with
APT (Bodie et al. 2008):

1. Identifying the factors: As APT mentions nothing about the factors, they
have to be identified empirically. These factors are usually macroeconomic
factors, like stock market return, inflation, business cycle, and so on. The
main problem in using macroeconomic factors is that factors are usually not
independent of each other. The identification of the factors is often carried
out by factor analysis. However, factors identified by factor analysis cannot
necessarily be interpreted in an economically meaningful way.
2. Estimating factor coefficients: In order to estimate the coefficients in a
multivariate linear regression model, a general version of Equation (3)
is used.
3. Estimating the factor premiums: The estimation of the factor premiums
is based on historical data, taking the average of the historical time-series
data of the premiums of the factor portfolios.
4. Pricing with APT: Equation (2) is used for calculating the expected return of
any asset by substituting the appropriate variables into the equation.
Fama-French three-factor model
Fama and French proposed a multifactor model in 1996, in which they used
corporate indicators as factors instead of macroeconomic factors, since they found
that these factors better describe the systemic risk of assets. Fama and French (1996)
extended the index model by adding the firm size and the book-to-market ratio as
return-generating factors to the market portfolio returns (Fama and French, 1996).
The firm size factor was constructed by taking the difference between the returns of
small and large firms (rSMB). The name of the variable was SMB, which is derived from
"small minus big". The book-to-market factor was calculated by taking the difference
between firms' returns that have a high and low book-to-market ratio (rHML). The name
of the variable was HML, which is derived from "high minus low".

Their model was the following:

Here, ai is a constant, which shows the abnormal rate of return, rf is the risk-free
return, and biHML is the ith asset's sensitivity to the book-to-market factor, while biSMB
is the ith asset's sensitivity to the factor of size, biM is the sensitivity of the ith stock's
risk premium to the market index factor, (rM-rf) is the risk premium of this factor, and
ei is the asset's non-systemic, firm-specific risk with zero mean.

In the following section, we will learn the implementation of the previously
described models with the help of R.

**Data selection**

Here, we only present how the time series of stock prices and other relevant information
can be acquired and used for the factor model's estimations.
We used the quantmod package to collect the database.
Here is how it works in R:

````r
library(quantmod)
stocks <- stockSymbols()
```

As a result, we need to wait for a few seconds while data is fetched, and then we can
see the output:

```
Fetching AMEX symbols...
Fetching NASDAQ symbols...
Fetching NYSE symbols...
Factor Models
```

Now, we have a data frame R object that contains about 6,500 stocks that are traded
on different exchanges such as AMEX, NASDAQ, or NYSE. In order to see the
variables that the dataset contains, we can use the str command:

```r
str(stocks)
```

```
'data.frame': 6551 obs. of 8 variables:
$ Symbol : chr "AA-P" "AAMC" "AAU" "ACU" ...
$ Name : chr "Alcoa Inc." "Altisource Asset Management Corp"...
$ LastSale : num 87 1089.9 1.45 16.58 16.26 ...
$ MarketCap: num 0.00 2.44e+09 9.35e+07 5.33e+07 2.51e+07 ...
$ IPOyear : int NA NA NA 1988 NA NA NA NA NA NA ...
$ Sector : chr "Capital Goods" "Finance" "Basic Industries"...
$ Industry : chr "Metal Fabrications" "Real Estate"...
$ Exchange : chr "AMEX" "AMEX" "AMEX" "AMEX" ...
```
We can drop the variables that we don't really need and include the information
about market capitalization and the book value of the company coming from
a different database as new variables since we will need them to estimate the
Fama-French model:

```r
stocks[1:5, c(1, 3:4, ncol(stocks))]
```

```
Symbol LastSale MarketCap BookValuePerShare
1 AA-P 87.30 0 0.03
2 AAMC 985.00 2207480545 -11.41
3 AAU 1.29 83209284 0.68
4 ACU 16.50 53003808 10.95
5 ACY 16.40 25309415 30.13
```

We will also need the time series of the risk-free return, which will be quantified in
this calculation by the one-month USD LIBOR rate:

```r
library(Quandl)

LIBOR <- Quandl('FED/RILSPDEPM01_N_B',
start_date = '2010-06-01', end_date = '2014-06-01')
```

The Quandl package, the tseries package, and other packages can also be used to get the prices of stocks, and the S&P 500 index can be used
as the market portfolio.

We have a table with stock prices (a time series of approximately 5,000 stock prices
between June 1, 2010 to June 1, 2014). The first and last few columns look like this:

```r
d <- read.table("data.csv", header = TRUE, sep = ";")
d[1:7, c(1:5, (ncol(d) - 6):ncol(d))]
```
```
Date SP500 AAU ACU ACY ZMH ZNH ZOES ZQK ZTS
ZX
1 2010.06.01 1070.71 0.96 11.30 20.64 54.17 21.55 NA 4.45 NA NA
2 2010.06.02 1098.38 0.95 11.70 20.85 55.10 21.79 NA 4.65 NA NA
3 2010.06.03 1102.83 0.97 11.86 20.90 55.23 21.63 NA 4.63 NA NA
4 2010.06.04 1064.88 0.93 11.65 18.95 53.18 20.88 NA 4.73 NA NA
5 2010.06.07 1050.47 0.97 11.45 19.03 52.66 20.24 NA 4.18 NA NA
6 2010.06.08 1062.00 0.98 11.35 18.25 52.99 20.96 NA 3.96 NA NA
7 2010.06.09 1055.69 0.98 11.90 18.35 53.22 20.45 NA 4.02 NA NA
```

If we have the data saved on our hard drive, we can simply read it with the
read.table function.

```r
d <- d[, colSums(is.na(d)) == 0]
d <- d[, c(T, colMins(d[, 2:ncol(d)]) > 0)]
```
To use the colMins function, we apply the matrixStats package. Now, we can start
working with the data.

**Factor Models**

Estimation of APT with principal component analysis

In practice, it is not easy to carry out a factor analysis, because identifying the macro
variables that have an effect on the securities' return is difficult . In many cases, the latent factors that drive the returns are searched by principal component analysis.

From the originally downloaded 6,500 stocks, we can use the data of 4,015 stocks;
the rest were excluded because of missing values or 0 prices. Now, we omit the first
two columns because we do not need the dates in this section, and the S&P 500 is
considered as a separate factor in itself; hence, we do not include it in the principal
component analysis (PCA). After this, the log returns are computed.

```r
p <- d[, 3:ncol(d)]
r <- log(p[2:nrow(p), ] / p[1:(nrow(p) - 1), ])
```

There exists another way to calculate the log returns of a given asset, that is, by using
return.calculate(data, method="log") with the PerformanceAnalytics library.
As we have too many stocks, in order to carry out PCA, either we have to have data
of at least 25 years, or we need to reduce the number of stocks. It's hopeless for factor
models to remain stable for decades; hence, for illustration purposes, we choose to
select 10 percent of the stocks randomly and compute the model for this sample:

```r
r <- r[, runif(nrow(r)) < 0.1]
```

runif(nrow(r)) < 0.1 is a 4,013 dimension 0-1 vector, which chooses
approximately 10 percent of the columns (in our case, 393) from the table. We can
also use the following sample function for this:
pca <- princomp(r)

As a result, we receive a princomp class object, which has eight attributes, of which
the most important ones are the loading matrix and the sdev attributes, which
contain the standard deviations of the components. The first principal component is
the vector on which the data set has the maximum variance.
Let's check the standard deviations of the principal component:

```r
plot(pca$sdev)
```

The result is as follows:
We can see that the first five components are separated; consequently, five factors
should be chosen, but other factors also have significant standard deviations, so the
market cannot be explained by a few factors.
We can confirm this result by calling the factanal function, which estimates the
factor model with five factors:

```r
factanal(r, 5)
```

We notice that it takes much more time to perform this computation. Factor analysis
is related to PCA, but is a little more complicated from a mathematical aspect. As a
result, we get an object of class factanal, which has many attributes, but now, we
are only interested in the following part of the output:

```
Factor1 Factor2 Factor3 Factor4 Factor5
SS loadings 56.474 23.631 15.440 12.092 6.257
Proportion Var 0.144 0.060 0.039 0.031 0.016
Cumulative Var 0.144 0.204 0.243 0.274 0.290
```

Test of the hypothesis that 5 factors are sufficient.
The chi square statistic is 91756.72 on 75073 degrees of freedom.The p-value is 0

Factor Models

This output shows that the factor model with five factors fits, but the explained
variance is only approximately 30 percent, which means that the model should be
extended with other factors as well.
