---
title: "Implementing Fama-French Factor model in R"
date: 2019-01-23
categories: [quantitative finance, pricing, factor models, r]
tags: [factor model, Fama-French, model implementation, asset pricing, R, statistics]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Implementing Fama-French Factor model in R"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---
PP 48-56 Implementing Fama-French Factor model in R


{% highlight r linenos %}
library(tidyverse)
df <- read_csv("some_file.csv")
head(df)
{% endhighlight %}
