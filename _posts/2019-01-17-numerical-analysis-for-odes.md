---
title: "Numerical Analysis for Ordinary Differential Equations : RBC agglomeration"
date: 2019-01-17
tags: [numerical analysis, ode, scilab, data science]
header:
  image: "/images/1- numerical analysis for ODEs/RBC banner2.jpg"
excerpt: "Here, I model Red Blood Cell (Haematocyte) agglomeration using scilab"
mathjax: true
---

# H1 Heading

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
3. Third

Python code block:
```python
import numpy as np

def test_function(x,y):
  z=np.sum(x,y)
return z
```

R code block:
```r
library(tidyverse)
df <- read_csv("some_file.csv")
head(df)
```

C code block:
```c
include <stdio.h>
# DEFINE code = 6
```

C++ code block:
```c++
int m,n,o;
m=m++  
```

VBA code block:
```vb
Public Sub test()
Dim a As String
a="lol"
MsgBox a
```

matlab code block:
```matlab
function price = EuPutExpl(SO,K,r,T,sigma,Smax,dS,dt)

%set up grid and adjust increments if necessary
M=round(Smax/dS)
dS=Smax/M
```

Here's some inline code `x+y`

Here's an image:
<img src="{{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/image1.jpg" alt="scilab numerical analysis plot" class="full">


Here's another image using Kramdown:
![alt]({{ site.url }}{{ site.baseurl }}/images/1- numerical analysis for ODEs/image1.jpg)
{: .full}

Here's some math :

$$z=x+y$$

You can also put it inline $$z=x+y$$
