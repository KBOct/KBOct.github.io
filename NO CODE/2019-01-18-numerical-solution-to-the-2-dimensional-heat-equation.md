---
title: "Numerical solution to the 2-dimensional heat equation (French)"
date: 2019-01-19
categories: [numerical analysis, python 2]
tags: [numerical analysis, pde, finite elements, python]
header:
  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Here, I compute the Numerical solution to the 2-dimensional heat equation using the finite elements method in Python"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---


{% highlight python linenos %}
import numpy as np

def test_function(x,y):
  z=np.sum(x,y)
return z
{% endhighlight %}
