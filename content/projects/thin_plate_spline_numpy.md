+++
title = "Numpy-enabled Thin-Plate Spline"
description = "A single python file with an object class that implements a SUPER-fast version of a thin-plate spline all in native NumPy."
author = "Mike Nute"
date = "2023-01-08"
tags = ["python", "spatial statistics", "bioinformatics"]
# categories = ["themes", "syntax"]
+++

Github Gist: [MGNute/thin-plate-spline-numpy.py](https://gist.github.com/MGNute/5bf4fce2c23ac5c131e94e6b437fcc64)

There are a lot of different spatial smoothing methods out there with different pros and cons, although for my money one of the neatest is the [thin-plate spline](https://en.wikipedia.org/wiki/Thin_plate_spline). That is the 2-D analog of a cubic spline in one variable, and it has a nice interpretation as an ANOVA model as well as a Bayesian interpolation of the nearby data points. The problem with splines like this though is that with $n$ data points #(x_1,y_1,f_1),...,(x_n,y_n,f_n)$ the inference step for a given $(x^*,y^*)$, $\hat{f}(x^*,y^*)$ is $\scriptO(n)$ because it requires computing a kernel function $K(x^*, x_i )$ for every data point $x_i$ in the training set. 

So computationally they become quite cumbersome when the data volume gets large. I had trouble finding a good implementation of the TPS that could handle a large volume of data (i.e. something like $n=10^5$ to $10^6$) without either taking a long time or requiring a custom implementation in C that needed to be compiled and installed and everything. So I implemented it myself using functions that were strictly native to NumPy, so all the kernel computation is done in C using the NumPy library.

This module implements a thin-plate spline using a Kernel function that accepts a vector of {x_1}-values and a vector of {x_2}-values and produces the ( |X_1| x |X_2| ) kernel matrix. This function though is written in a creative way so the entire generation of the kernel matrix happens in native python, for maximum speed. 

This object class has functions that do the following:

 - Initialize the object, accepting dependent (Y) variables as well as independent (X, namely (x_1, x_2)). 
 - Initialize one from a previouls TPS object. 
 - Compute the kernel matrix (as noted).
 - Solves for parameters (particularly in semi-parameteric case)
 - Computes fitted Y-hat values.
 - Reports diagnostics: A) Sample Size, B) Mean residual value, C) Std. dev. of residual value.
 - Predicts for a new set of X-values based on the fitted model.
 - Will do prediction over a full grid, given grid input params.
 - Saves/Loads a thin-plate spline object to disk.

