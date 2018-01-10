
<!-- README.md is generated from README.Rmd. Please edit that file -->

# R/`shifttx`

[![Travis-CI Build
Status](https://travis-ci.org/nhejazi/shifttx.svg?branch=master)](https://travis-ci.org/nhejazi/shifttx)
[![AppVeyor Build
Status](https://ci.appveyor.com/api/projects/status/github/nhejazi/shifttx?branch=master&svg=true)](https://ci.appveyor.com/project/nhejazi/shifttx)
[![Coverage
Status](https://img.shields.io/codecov/c/github/nhejazi/shifttx/master.svg)](https://codecov.io/github/nhejazi/shifttx?branch=master)
[![Project Status: WIP - Initial development is in progress, but there
has not yet been a stable, usable release suitable for the
public.](http://www.repostatus.org/badges/latest/wip.svg)](http://www.repostatus.org/#wip)
[![MIT
license](http://img.shields.io/badge/license-MIT-brightgreen.svg)](http://opensource.org/licenses/MIT)

> Targeted Learning of Continuous Interventions with Stochastic
> Treatment Regimes

**Authors:** [Nima Hejazi](http://nimahejazi.org) and [David
Benkeser](https://www.benkeserstatistics.com/)

-----

## What’s `shifttx`?

`shifttx` is an R package that makes it easy to compute targeted minimum
loss-based estimates of the population-level causal effects of
interventions based on stochastic mechanisms for treatment assignment.

  - Original estimator and iterative algorithm proposed in Muñoz and van
    der Laan (2012)
  - One-step estimation procedure and algorithm introduced in Díaz and
    van der Laan (2017).
  - See van der Laan and Rose (2017) for a discussion on recent
    developments in Targeted Learning.
  - See van der Laan and Rose (2011) for an introduction to Targeted
    Learning.

-----

## Installation

Install the most recent *stable release* from GitHub via
[`devtools`](https://www.rstudio.com/products/rpackages/devtools/):

``` r
devtools::install_github("nhejazi/shifttx")
```

-----

## Example

To illustrate how `shifttx` may be used to ascertain the effect of a
treatment, consider the following example:

``` r
library(condensier)
#> condensier
#> The condensier package is still in beta testing. Interpret results with caution.
library(shifttx)
#> shifttx: Targeted Learning with Stochastic Interventions
#> Version: 0.0.8

# simulate simple data for tmle-shift sketch
set.seed(429153)
n_obs <- 1000
n_w <- 1
a1_mean <- 2
a0_mean <- 0

## baseline covariate -- simple, binary
W <- as.numeric(replicate(n_w, rbinom(n_obs, 1, 0.5)))

## set and organize treatment based on baseline W
A1 <- rnorm(length(which(W == 1)), mean = a1_mean, sd = 1)
A0 <- rnorm(length(which(W == 0)), mean = a0_mean, sd = 1)
A <- rep(NA, n_obs)
A[which(W == 0)] <- A0
A[which(W == 1)] <- A1

# create outcome
Y <- A + W + rnorm(n_obs)

# induce censoring
C <- rbinom(n_obs, 1, plogis(W))

# fit the TMLE
tmle_shift <- tmle_shifttx(W = W, A = A, Y = Y, delta = 0.5,
                           fluc_method = "standard",
                           args = list(
                             g_fit = list(fit_type = "glm",
                                          nbins = 25,  bin_method = "dhist",
                                          bin_estimator = speedglmR6$new(),
                                          parfit = FALSE),
                             Q_fit = list(fit_type = "glm",
                                          glm_formula = "Y ~ .")
                          ))

# examine the results
tmle_shift
#> $psi
#> [1] 2.056182
#> 
#> $var
#> [1] 0.004724677
#> 
#> $msg
#> [1] "EIF mean < 1e-07 (sufficiently low)."

# compute the confidence interval and view the results
(ci_shift <- confint(tmle_shift))
#>   lwr_CI      est   upr_CI 
#> 1.921461 2.056182 2.190902
```

-----

## Issues

If you encounter any bugs or have any specific feature requests, please
[file an issue](https://github.com/nhejazi/shifttx/issues).

-----

## Funding

The development of this software was supported in part through a grant
from the National Library of Medicine of the National Institutes of
Health (T32 LM012417).

-----

## License

© 2017-2018 [Nima S. Hejazi](http://nimahejazi.org)

The contents of this repository are distributed under the MIT license.
See below for details:

    MIT License
    
    Copyright (c) 2017 Nima S. Hejazi
    
    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to deal
    in the Software without restriction, including without limitation the rights
    to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:
    
    The above copyright notice and this permission notice shall be included in all
    copies or substantial portions of the Software.
    
    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
    SOFTWARE.

-----

## References

<div id="refs" class="references">

<div id="ref-diaz2017stochastic">

Díaz, Iván, and Mark J van der Laan. 2017. “Stochastic Treatment
Regimes.” In *Targeted Learning in Data Science: Causal Inference for
Complex Longitudinal Studies*, 167–80. Springer Science & Business
Media.

</div>

<div id="ref-munoz2012population">

Muñoz, Iván Díaz, and Mark J van der Laan. 2012. “Population
Intervention Causal Effects Based on Stochastic Interventions.”
*Biometrics* 68 (2). Wiley Online Library:541–49.

</div>

<div id="ref-vdl2011targeted">

van der Laan, Mark J, and Sherri Rose. 2011. *Targeted Learning: Causal
Inference for Observational and Experimental Data*. Springer Science &
Business Media.

</div>

<div id="ref-vdl2017targeted">

———. 2017. *Targeted Learning in Data Science: Causal Inference for
Complex Longitudinal Studies*. Springer Science & Business Media.

</div>

</div>
