
<!-- README.md is generated from README.Rmd. Please edit that file -->

# sims

<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.com/lifecycle/#experimental)
[![Travis-CI Build
Status](https://travis-ci.com/poissonconsulting/sims.svg?branch=master)](https://travis-ci.com/poissonconsulting/sims)
[![AppVeyor build
status](https://ci.appveyor.com/api/projects/status/github/poissonconsulting/sims?branch=master&svg=true)](https://ci.appveyor.com/project/poissonconsulting/sims)
[![Coverage
Status](https://img.shields.io/codecov/c/github/poissonconsulting/sims/master.svg)](https://codecov.io/github/poissonconsulting/sims?branch=master)
[![License:
GPL3](https://img.shields.io/badge/License-GPL3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0.html)
<!-- [![Tinyverse status](https://tinyverse.netlify.com/badge/sims)](https://CRAN.R-project.org/package=sims) -->
<!-- [![CRAN status](https://www.r-pkg.org/badges/version/sims)](https://cran.r-project.org/package=sims) -->
<!-- ![CRAN downloads](http://cranlogs.r-pkg.org/badges/sims) -->
<!-- badges: end -->

sims is an R package to generate datasets from
[JAGS](http://mcmc-jags.sourceforge.net) or R code for use in simulation
studies. The datasets are returned as an
[nlists](https://github.com/poissonconsulting/nlist) object and/or saved
to file as individual .rds files.

## Installation

To install the developmental version from
[GitHub](https://github.com/poissonconsulting/sims)

``` r
# install.packages("remotes")
remotes::install_github("poissonconsulting/sims")
```

To install the latest developmental release from the Poisson drat
[repository](https://github.com/poissonconsulting/drat)

``` r
# install.packages("drat")
drat::addRepo("poissonconsulting")
install.packages("sims")
```

## Demonstration

### Simulate Data

By default, `sims_simulate()` returns the simulated datasets in the form
of an [nlists](https://github.com/poissonconsulting/nlist) object.

``` r
library(sims)
set.seed(10)
sims_simulate("a ~ dunif(0,1)", nsims = 2L)
#> $a
#> [1] 0.228673
#> 
#> an nlists object of 2 nlist objects each with 1 natomic element
```

If, however, `path` is provided then each nlist object is saved as an
`.rds` files. The information used to generate the datasets is saved in
`.sims_args.rds`.

``` r
set.seed(10)
sims_simulate("a ~ dunif(0,1)", nsims = 2L, path = tempdir(), exists = NA)
#> [1] TRUE
sims_data_files(tempdir())
#> [1] "data0000001.rds" "data0000002.rds"
```

The fact that the arguments to `sims_simulate()` are saved to file
allows additional datasets to be generated using `sims_add()`.

``` r
sims_add(tempdir(), nsims = 3L)
#> [1] "data0000003.rds" "data0000004.rds" "data0000005.rds"
sims_data_files(tempdir())
#> [1] "data0000001.rds" "data0000002.rds" "data0000003.rds" "data0000004.rds"
#> [5] "data0000005.rds"
```

If the user wishes to duplicate the datasets then they can either
regenerate them by specifying a different path but the same seed.
Alternatively, they can copy the existing `.sims.rds` and datasets files
to a new directory using `sims_copy()`

``` r
sims_copy(path_from = tempdir(), path_to = paste0(tempdir(), "_copy"))
#> [1] "data0000001.rds" "data0000002.rds" "data0000003.rds" "data0000004.rds"
#> [5] "data0000005.rds"
```

A user can check that all the datasets specified in `.sims.rds` are
present using `sims_check()`.

``` r
sims_check(path = paste0(tempdir(), "_copy"))
```

``` r
file.remove(file.path(paste0(tempdir(), "_copy"), "data0000005.rds"))
#> [1] TRUE

sims_check(path = paste0(tempdir(), "_copy"))
#> Number of data files (4) does not match number of simulations (5).
```

## Parallelization

Parallelization is achieved using the
[future](https://github.com/HenrikBengtsson/future) package.

To use all available cores on the local machine simply execute the
following code before calling `sims_simulate()`.

    library(future)
    plan(multisession)

## Contribution

Please report any
[issues](https://github.com/poissonconsulting/sims/issues).

[Pull requests](https://github.com/poissonconsulting/sims/pulls) are
always welcome.

Please note that this project is released with a [Contributor Code of
Conduct](https://github.com/poissonconsulting/sims/blob/master/CODE_OF_CONDUCT.md).
By contributing, you agree to abide by its terms.
