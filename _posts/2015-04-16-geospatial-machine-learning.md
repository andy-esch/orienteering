---
layout:     post
title:      Geospatial Machine Learning
date:       2015-04-15 13:00:00
summary:    Paper summary and thoughts
categories: research paper
---

To kick off our new blog, we'll look at a paper on machine learning for spatio-temporal data. The paper is located [here](http://www.iemss.org/iemss2008/uploads/Main/S04-09-Kanevski_et_al-IEMSS2008.pdf).

## Summary

Machine learning translates well to analyzing geospatial data. Like other machine learning analyses, the data set is taken to more than the geospatial coordinates, often involving other parameters associated with the location data such as elevation, slope, population density, demographics, income, etc. The common machine learning algorithms involved in Artificial Neural Networks (ANN) and Support Vector Machines (SVM) are discussed.

Tools are often paired together to balance the strengths of one with the weaknesses of another. For instance, k-NN, which the authors discuss under ANN, "does not provide details about patterns and their structures if they are present..." It instead looks at residuals of the model from the original distribution. Variography is a good complementary tool from geostatistics that can detect the spatial correlations and anisotropies in the data.

+ SVM are used to analyze data and recognize patterns through nonlinear classification and regression with the aim of minimizing empirical risk and model complexity. For geospatial data, it "generalizes well"

+ ANN 

The paper ends with this sentence: "Finally it should be noted, that being a data driven models they need deep expert knowledge in order to be applied correctly and efficiently starting from data pre-processing to the interpretation and justification of the results."

### Summary
Light on the actual uses cases, but heavy on the "high potential" of using machine learning in geospatial technology. It is interesting to think about how to incorporate ML into our model. Functions from python can easily be ported to PL/Python scripts and run for users. [Pysal](https://pysal.readthedocs.org/en/latest/library/spreg/ml_lag_regimes.html) offers an interesting set of tools that could be an ideal starting place.

Spatio-temporal data were only mentioned as a complicating factor in analysis. ML techniques on this type of data might be a good follow up.

### "Particularities" of geospatial data

Geospatial data often has one or more of these properties:

1. Nonlinearity -- linear models have a limited applicability
2. Spatial and temporal non-stationarity -- spatio-temporal stationarity cannot be accepted (e.g., things change)
3. Multi-scale variability -- things cluster differently at different scales
4. Presence of noise and extreme outliers

This all leads to complicate the analysis, modeling, and visualization of geo data. Leads to the necessity of analyzing in a high-dimensional setting, oftentimes including more than just the direct geographical information.

### Interesting bits

Breakdown of problems and normal ways to solve them via machine learning and geostatistics:

+ Spatial predictions/interpolations: deterministic interpolators, geostatistics, machine learning. Spatial predictions in a high dimensional geo-feature space – machine learning.
+ Modelling and spatial predictions with uncertainties (e.g. taking into account measurement errors): geostatistics, machine learning.
+ Multivariate joint predictions of several variables: geostatistics (co-kriging), machine learning (multi-task learning).
+ Risk mapping – modelling of local probability density function: geostatistics (indicator kriging, simulations), machine learning (Mixture Density Networks).
+ Modelling of spatial variability and uncertainty, conditional simulations (spatial Monte Carlo simulations): geostatistical conditional stochastic simulations (sequential Gaussian simulations, indicator simulations, etc.).
+ Optimisation of monitoring networks (spatial sampling design/redesign): space filling models, geostatistics (kriging, simulations), machine learning (Support Vector Machines). The basic idea of using Support Vector Machines for spatial sampling design is that only support vectors are important measurement points contributing to the solution of mapping problem [Vapnik, 1999].
+ Data mining in a high dimensional geo-feature space: machine learning (supervised and unsupervised learning algorithms).

### New concepts to me

#### Variography

Variography is a geospatial version of analyzing a variogram, an analysis tool for looking at spatial structures, anisotropies (difference in direction), and different levels of scale in a geospatial dataset

\\[ \gamma(h) = \frac{1}{2}\frac{1}{N(h)}\sum_{N(h)} (i - j)^2 \\]

![variogram from NKU](/images/variogram.gif)

#### Support Vector Machines

"[Support Vector Machines](http://en.wikipedia.org/wiki/Support_vector_machine) are supervised learning models with associated learning algorithms that analyze data and recognize patterns, used for classification and regression analysis." They can perform linear or nonlinear classification.