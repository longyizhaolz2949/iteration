writing functions
================
Longyi Zhao
2023-10-26

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'
    ## 
    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6, 
  out.width = "90%"
)

theme_set (theme_minimal() +theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis", 
  ggplots.continuous.fill = "viridis"
)

scale_colour_discrete = scale_colour_viridis_d
scale_fill_disrete = scale_fill_viridis_d
```

output on this page is fixed

``` r
set.seed(1)
```

``` r
x_vec = rnorm(20, mean = 5, sd =0.3)
```

``` r
(x_vec - mean(x_vec))/sd(x_vec)
```

    ##  [1] -0.894579096 -0.007534108 -1.123622566  1.538189109  0.152185414
    ##  [6] -1.107022329  0.325106999  0.599834216  0.421851526 -0.543016965
    ## [11]  1.446758183  0.218251901 -0.888870682 -2.633686248  1.023162590
    ## [16] -0.257822640 -0.226349080  0.824866429  0.690604708  0.441692638

x is the input everytime when it see x, put into the function

``` r
z_score = function(x) {
  z = (x-mean(x))/sd(x)
  if (!is.numeric(x)) {
    stop("Argument should be numbers")
  } else if (length(x) < 2) {
    stop("You need at least 2 numbers to get z score")
  }
  z # return z
}
```

check that this works

``` r
z_score(x = x_vec)
```

    ##  [1] -0.894579096 -0.007534108 -1.123622566  1.538189109  0.152185414
    ##  [6] -1.107022329  0.325106999  0.599834216  0.421851526 -0.543016965
    ## [11]  1.446758183  0.218251901 -0.888870682 -2.633686248  1.023162590
    ## [16] -0.257822640 -0.226349080  0.824866429  0.690604708  0.441692638

``` r
z_score(x=3) # single number no sd 
```

    ## Error in z_score(x = 3): You need at least 2 numbers to get z score

``` r
z_score(x = c("my", "name")) # character variable no mean and sd 
```

    ## Warning in mean.default(x): argument is not numeric or logical: returning NA

    ## Error in x - mean(x): non-numeric argument to binary operator

``` r
z_score(x = c(TRUE, FALSE, TRUE, TRUE))
```

    ## Error in z_score(x = c(TRUE, FALSE, TRUE, TRUE)): Argument should be numbers

## Multiple output

write a function that returns the mean and sd from a sample of numbers
find a way to return multiple things at the same time, for example both
mean and sd, for example return a dataframe using tibble

``` r
mean_and_sd = function(x) {
   z = (x-mean(x))/sd(x)
  if (!is.numeric(x)) {
    stop("Argument should be numbers")
  } else if (length(x) < 2) {
    stop("You need at least 2 numbers to get z score")
  }
  
  mean_x = mean(x)
  sd_x = sd(x)
  
  tibble(
    mean = mean_x, 
    sd = sd_x
  )
  
}
```

check

``` r
mean_and_sd(x_vec)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  5.06 0.274

## Multiple Inputs: start getting means and sds

``` r
x_vec = rnorm(n = 30, mean = 5, sd = 0.5)

tibble(
  mean = mean(x_vec), 
  sd = sd(x_vec)
)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  5.02 0.391

lets write a function that uses `n`, a true mean and true sd as inputs
mu = mean sigma = sd

``` r
sim_mean_sd = function(n_obs, mu, sigma) {
  
  x_vec = rnorm(n = n_obs, mean = mu, sd = sigma)

  tibble(
    mean = mean(x_vec), 
    sd = sd(x_vec)
  )

}

sim_mean_sd(n_obs = 30, mu = 5, sigma = 0.5)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  5.06 0.511

``` r
# get different mean and sd each time run the function 

sim_mean_sd (12, 12, 4) # positional matching
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  12.6  3.28

``` r
sim_mean_sd (mu = 12, n_obs = 12, sigma = 4) # can name in different order 
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  11.7  3.72
