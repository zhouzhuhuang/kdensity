
<!-- README.md is generated from README.Rmd. Please edit that file -->
kdensity <img src="man/figures/logo.png" align="right" width="247" height="70" />
=================================================================================

[![Build Status](https://travis-ci.org/JonasMoss/kdensity.svg?branch=master)](https://travis-ci.org/JonasMoss/kdensity) [![AppVeyor Build Status](https://ci.appveyor.com/api/projects/status/github/JonasMoss/kdensity?branch=master&svg=true)](https://ci.appveyor.com/project/JonasMoss/kdensity) [![CRAN\_Status\_Badge](http://www.r-pkg.org/badges/version/kdensity)](http://cran.r-project.org/package=kdensity) [![Coverage Status](https://codecov.io/gh/JonasMoss/kdensity/branch/master/graph/badge.svg)](https://codecov.io/gh/JonasMoss/kdensity?branch=master) [![DOI](https://zenodo.org/badge/120678148.svg)](https://zenodo.org/badge/latestdoi/120678148)

An `R` package for univariate kernel density estimation with parametric starts and asymmetric kernels.

Overview
--------

kdensity is an implementation of univariate kernel density estimation with support for parametric starts and asymmetric kernels. Its main function is `kdensity`, which is has approximately the same syntax as `stats::density`. Its new functionality is:

-   `kdensity` has built-in support for many *parametric starts*, such as `normal` and `gamma`, but you can also supply your own.
-   It supports several asymmetric kernels ones such as `gcopula` and `gamma` kernels, but also the common symmetric ones. In addition, you can also supply your own kernels.
-   A selection of choices for the bandwidth function `bw`, again including an option to specify your own.
-   The returned value is callable: The density estimator returns a density function when called.

A reason to use `kdensity` is to avoid *boundary bias* when estimating densities on the unit interval or the positive half-line. Asymmetric kernels such as `gamma` and `gcopula` are designed for this purpose. The support for parametric starts allows you to easily use a method that is often superior to ordinary kernel density estimation.

Installation
------------

From inside `R`, use one of the following commands:

``` r
# For the CRAN release
install.packages("kdensity")
# For the development version from GitHub:
# install.packages("devtools")
devtools::install_github("JonasMoss/kdensity")
```

Call the `library` function and use it just like `stats:density`, but with optional additional arguments.

``` r
library("kdensity")
plot(kdensity(mtcars$mpg, start = "normal"))
```

Description
-----------

Kernel density estimation with a *parametric start* was introduced by Hjort and Glad in [Nonparametric Density Estimation with a Parametric Start (1995)](https://projecteuclid.org/euclid.aos/1176324627). The idea is to start out with a parametric density before you do your kernel density estimation, so that your actual kernel density estimation will be a correction to the original parametric estimate. This is a good idea because the resulting estimator will be better than an ordinary kernel density estimator whenever the true density is close to your suggestion; and the estimator can be superior to the ordinary kernal density estimator even when the suggestion is pretty far off.

In addition to parametric starts, the package implements some *asymmetric kernels*. These kernels are useful when modelling data with sharp boundaries, such as data supported on the positive half-line or the unit interval. Currently we support the following asymmetric kernels:

-   Jones and Henderson's *Gaussian copula KDE*, from [Kernel-Type Density Estimation on the Unit Interval (2007)](https://academic.oup.com/biomet/article-abstract/94/4/977/246269). This is used for data on the unit interval. The bandwidth selection mechanism described in that paper is implemented as well. This kernel is called `gcopula`.

-   Chen's two *beta kernels* from [Beta kernel estimators for density functions (1999)](https://www.sciencedirect.com/science/article/pii/S0167947399000109). These are used for data supported on the on the unit interval, and are called `beta` and `beta_biased`.

-   Chen's two *gamma kernels* from [Probability Density Function Estimation Using Gamma Kernels (2000)](https://link.springer.com/article/10.1023/A:1004165218295). These are used for data supported on the positive half-line, and are called `gamma` and `gamma_biased`.

These features can be combined to make asymmetric kernel densities estimators with parametric starts, see the example below. The package contains only one function, `kdensity`, in addition to the generics `plot`, `points`, `lines`, `summary`, and `print`.

Usage
-----

The function `kdensity` takes some `data`, a kernel `kernel` and a parametric start `start`. You can optionally specify the `support` parameter, which is used to find the normalizing constant.

The following example uses the data set plots both a gamma-kernel density estimate with a gamma start (black) and the the fully parametric gamma density. The underlying parameter estimates are always maximum likelood.

``` r
library("kdensity")
kde = kdensity(airquality$Wind, start = "gamma", kernel = "gamma")
plot(kde, main = "Wind speed (mph)")
lines(kde, plot_start = TRUE, col = "red")
rug(airquality$Wind)
```

<img src="man/figures/README-example-1.png" width="750px" />

Since the return value of `kdensity` is a function, it is callable, as in:

``` r
kde(10)
#> [1] 0.09980471
```

You can access the parameter estimates by using `coef`. You can also access the log likelihood (`logLik`), AIC and BIC of the parametric start distribution.

``` r
coef(kde)
#>     shape      rate 
#> 7.1872898 0.7217954
logLik(kde)
#> 'log Lik.' 12.33787 (df=2)
AIC(kde)
#> [1] -20.67574
```

References
----------

-   [Hjort, Nils Lid, and Ingrid K. Glad. "Nonparametric density estimation with a parametric start." The Annals of Statistics (1995): 882-904.](https://projecteuclid.org/euclid.aos/1176324627).

-   [Jones, M. C., and D. A. Henderson. "Miscellanea kernel-type density estimation on the unit interval." Biometrika 94.4 (2007): 977-984.](https://academic.oup.com/biomet/article-abstract/94/4/977/246269).

-   [Chen, Song Xi. "Probability density function estimation using gamma kernels." Annals of the Institute of Statistical Mathematics 52.3 (2000): 471-480.](https://link.springer.com/article/10.1023/A:1004165218295).

-   [Chen, Song Xi. "Beta kernel estimators for density functions." Computational Statistics & Data Analysis 31.2 (1999): 131-145.](https://www.sciencedirect.com/science/article/pii/S0167947399000109)
