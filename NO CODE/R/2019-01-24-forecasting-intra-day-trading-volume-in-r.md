---
title: "Forecasting intra day trading volume in R"
date: 2019-01-24
categories: [quantitative finance, prediction, trading, r]
tags: [forecasting, prediction, intra-day trading, trading volume, R, statistics]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Forecasting intra day trading volume in R"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---
PP 59-74 Forecasting intra day trading volume in R

R code block:
{% highlight r linenos %}
library(tidyverse)
df <- read_csv("some_file.csv")
head(df)
{% endhighlight %}
