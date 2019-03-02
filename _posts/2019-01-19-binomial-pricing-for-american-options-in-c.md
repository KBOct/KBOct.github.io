---
title: "Binomial pricing for American options in C"
date: 2019-01-19
categories: [quantitative finance, pricing, c]
tags: [quantitative finance, binomial tree, CRR, Cox Ross Rubinstein, american options, pricing, C]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Binomial pricing for American options in C"
toc: true
toc_label: "Contents"
toc_icon: "list-ul"  # corresponding Font Awesome icon name (without fa prefix
toc_sticky: true
mathjax: true
---

{% highlight c linenos %}

#include <math.h>
#include <stdio.h>

#define Taille 1000

typedef double Etat[Taille + 1];

typedef struct Option {
	double TauxAnnuel;
	double Sigma;
	double T;
	double ValeurInitiale;
	double K;
} Option;

double plus(double x)
{
	return (x>0?x:0);
}

double Prix(Option opt)
{
	double r, a, b, proba, p1, p2, tmp, ro, xmax;
	Etat P, roi;
	long i, j, N;

	/ * Calcul des parametres de Cox Ross * /
	N = 500;
	r = opt.TauxAnnuel * opt.T / N;
	a = (1 + r) * exp(-opt.Sigma * sqrt(opt.T / N)) - 1;
	b = (1 + r) * exp(opt.Sigma * sqrt(opt.T / N)) - 1;
	proba = (b - r) / (b - a);
	/ *  par souci d’efficacite on calcule
		le maximum de choses en dehors de la boucle principale * /
	ro = (1 + a) / (1 + b);
	roi[0] = 1.0;
	for (i = 1; i <= N; i++)
		roi[i] = ro * roi[i - 1];
	p1 = (1 - proba) / (1 + r);
	p2 = proba / (1 + r);
	/ * A un instant j, P[i] approxime P(j,x(1+a)^i (1+b)^(N-j-i)) * /
	/ * en N : P[i] = (K-x(1+a)^i (1+b)^(N-i))+ * /
	xmax = opt.ValeurInitiale * pow(1 + b, N);
	for (i = 0; i <= N; i++)
		P[i] = plus(opt.K - xmax * roi[i]);
	for (j = 1; j <= N; j++) {
		xmax /= 1 + b;
		for (i = 0; i <= N - j; i++) {
			P[i] = p1 * P[i] + p2 * P[i + 1];
			tmp = plus(opt.K - xmax * roi[i]);
			if (tmp > P[i])
				P[i] = tmp;
		}
	}
	return (P[0]);
}

{% endhighlight %}
