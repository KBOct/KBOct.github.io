---
title: "Brennan-Schwartz American put pricing in C"
date: 2019-01-19
categories: [quantitative finance, pricing, c]
tags: [quantitative finance, Brennan-Schwartz, American put pricing, C, options, pricing]
#header:
#  image: "/images/2 - heat equation/heat.jpg"
excerpt: "Brennan-Schwartz American put pricing in C"
mathjax: true
author_profile: false
---


Here's the Brennan-Schwartz algorithm to price American put options in C:

{% highlight c linenos %}
#include <math.h>
#include <stdio.h>

#define NbrePasPrix 200
#define NbrePasTemps 200
#define Precision 0.01
#define NbreDeJoursDansAnnee 360

typedef long Date;
typedef double Montant;

typedef struct PutAmericain {
	Date DateContrat; /  en jours * /
	Date DateEcheance; / * en jours * /
	Montant PrixExercice;
} PutAmericain;

typedef double vecteur[NbrePasPrix];

typedef struct Modele {
	double r; / * taux d’interet annuel sans risque * /
	double sigma; / * volatilite annuelle * /
	double x0; / * Valeur initiale de l’EDS * /
} Modele;

double ObstaclePut(double x, PutAmericain Opt)
{
	double u = Opt.PrixExercice - exp(x);
	if (u > 0) return u; else return 0.0;
}

double Prix(long t, double x, PutAmericain option, Modele modele)
/ * Calcule le prix de l’"option" dans le "modele" a l’instant "t"
si le prix du sous jacent a cet instant est "x". * /
{
	vecteur Obst, A, B, C, G;
	double alpha, beta, gamma, h, k, vv, temp, r, y, delta, Temps;
	long Indice, IndicePrix, IndiceTemps;
	double l = 2.0;/ * largeur de l’intervalle a considerer * /

	Temps = (double)(option.DateEcheance - t) / NbreDeJoursDansAnnee;
	k = Temps / NbrePasTemps;
	r = modele.r;
	vv = modele.sigma * modele.sigma;
	h = 2 * l / NbrePasPrix;
	alpha = k * ((r - vv / 2.0) / (2.0 * h) - vv / (2.0 * h * h));
	beta = 1 + k * (r + vv / (h * h));
	gamma = k * ((vv / 2.0 - r) / (2.0 * h) - vv / (2.0 * h * h));

	for (IndicePrix = 0; IndicePrix < NbrePasPrix; IndicePrix++) {
	A[IndicePrix] = alpha;
	B[IndicePrix] = beta;
	C[IndicePrix] = gamma;
	}
		B[0] = beta + alpha;
		B[NbrePasPrix - 1] = beta + gamma;
		G[IndicePrix - 1] = 0.0;

	for (IndicePrix = NbrePasPrix - 1; IndicePrix >= 1; IndicePrix--)
		B[IndicePrix - 1] -= C[IndicePrix - 1] * A[IndicePrix] / B[IndicePrix];
	for (IndicePrix = 0; IndicePrix < NbrePasPrix; IndicePrix++) A[IndicePrix] /= B[IndicePrix];
	for (IndicePrix = 1; IndicePrix < NbrePasPrix; IndicePrix++) C[IndicePrix - 1] /= B[IndicePrix];

	y = log(x);
	for (IndicePrix = 1; IndicePrix <= NbrePasPrix; IndicePrix++)
	Obst[IndicePrix - 1] = ObstaclePut(y - l + IndicePrix * h, option);
	for (IndicePrix = 0; IndicePrix < NbrePasPrix; IndicePrix++)
	G[IndicePrix] = Obst[IndicePrix];

	for (IndiceTemps = 1; IndiceTemps <= NbrePasTemps; IndiceTemps++) {
		for (IndicePrix = NbrePasPrix - 1; IndicePrix >= 1; IndicePrix--)
			G[IndicePrix - 1] -= C[IndicePrix - 1] * G[IndicePrix];
		G[0] /= B[0];
		for (IndicePrix = 2; IndicePrix <= NbrePasPrix; IndicePrix++) {
			G[IndicePrix - 1] = G[IndicePrix - 1] / B[IndicePrix - 1]
									- A[IndicePrix - 1] * G[IndicePrix - 2];
			temp = Obst[IndicePrix - 1];
			if (G[IndicePrix - 1] < temp) G[IndicePrix - 1] = temp;
		}
	}
	Indice = NbrePasPrix / 2;
	delta = (G[Indice] - G[Indice - 1]) / h;
	return (G[Indice - 1] + delta * (Indice * h - l));
}

/ *  Exemple :
	PutAmericain contrat = {0,360,45};
	Modele modele = {0.09,0.3,45};
	printf("%f\n",Prix(0,45,contrat,modele));* /

{% endhighlight %}
