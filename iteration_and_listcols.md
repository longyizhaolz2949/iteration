iteration_and_listcols
================
Longyi Zhao
2023-10-31

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
set.seed(12345)
```

### List

``` r
vec_numeric = 5:8
vec_char = c("My", "name", "is", "Jeff")
vec_logical = c(TRUE, TRUE, TRUE, FALSE)

tibble(
  num = vec_numeric,
    char = vec_char
)
```

    ## # A tibble: 4 × 2
    ##     num char 
    ##   <int> <chr>
    ## 1     5 My   
    ## 2     6 name 
    ## 3     7 is   
    ## 4     8 Jeff

different stuff with different length: not gonna work

``` r
l = list(
  vec_numeric = 1:5, 
  vec_char = LETTERS, 
  matrix = matrix(1:10, nrow = 5, ncol = 2),
  summary = summary (rnorm(100))
)
```

accessing lists

``` r
l$vec_char
```

    ##  [1] "A" "B" "C" "D" "E" "F" "G" "H" "I" "J" "K" "L" "M" "N" "O" "P" "Q" "R" "S"
    ## [20] "T" "U" "V" "W" "X" "Y" "Z"

``` r
l[[2]]
```

    ##  [1] "A" "B" "C" "D" "E" "F" "G" "H" "I" "J" "K" "L" "M" "N" "O" "P" "Q" "R" "S"
    ## [20] "T" "U" "V" "W" "X" "Y" "Z"

``` r
l[["summary"]]
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ## -2.3804 -0.5901  0.4837  0.2452  0.9004  2.4771

## for loops

``` r
list_norms = 
  list(
    a = rnorm(20, 3, 1),
    b = rnorm(20, 0, 7),
    c = rnorm(20, 20, 1),
    d = rnorm(20, -3, 1)
  )

is.list(list_norms)
```

    ## [1] TRUE

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

``` r
mean_and_sd(list_norms$a)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.05 0.984

``` r
mean_and_sd(list_norms$b)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 0.690  9.30

``` r
mean_and_sd(list_norms$c)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  19.8 0.910

``` r
mean_and_sd(list_norms$d)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.93  1.08

write a for loop

``` r
output = vector("list", length = 4)

for (i in 1:4) {
  output[[i]] = mean_and_sd(list_norms[[i]])
}
```

## Use ‘map’

use a map function to do the same thing

``` r
output_map = map(list_norms, mean_and_sd)

output_median = map(list_norms, median)
```

\##create a dataframe

``` r
listcol_df = 
  tibble(
    name = c("a", "b", "c", "d"), 
    samp = list_norms
  )
```

``` r
listcol_df |> pull(samp)
```

    ## $a
    ##  [1] 3.223925 1.843777 3.422419 1.675245 3.141084 2.463952 2.688394 4.556110
    ##  [9] 2.551967 3.321124 1.769828 1.675941 4.261242 4.319232 2.919246 2.494910
    ## [17] 2.947846 3.628861 5.180002 2.930983
    ## 
    ## $b
    ##  [1]  10.8140452   9.2501641   2.2550610  10.7166858  -2.9486779  -8.1117472
    ##  [7] -12.9175780   8.1012770 -14.8648492  -8.3722206  11.4953439   6.1855838
    ## [13]   3.6741312  -8.2926135  18.5905179  -7.3353960  -7.0778577   4.6824516
    ## [19]   0.9042411  -2.9580381
    ## 
    ## $c
    ##  [1] 18.85974 18.70628 19.40530 18.49919 20.01586 20.54017 18.45271 20.84965
    ##  [9] 20.89601 20.13869 18.38067 20.54840 20.19528 19.19350 19.89138 19.74905
    ## [17] 21.69935 19.65570 20.06777 19.34943
    ## 
    ## $d
    ##  [1] -3.4876385 -2.6968488 -3.2419740 -3.4817336 -3.9918029 -3.2806491
    ##  [7] -2.3669827 -4.2398183 -1.2356859 -3.0236799 -2.8000795 -1.6528072
    ## [13] -2.9639265 -2.1754189 -4.7026719 -2.5190498 -0.5164499 -2.5986350
    ## [19] -2.7848228 -4.8157124

``` r
mean_and_sd(listcol_df$samp[[1]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.05 0.984

``` r
mean_and_sd(listcol_df$samp[[2]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 0.690  9.30

``` r
mean_and_sd(listcol_df$samp[[3]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  19.8 0.910

``` r
mean_and_sd(listcol_df$samp[[4]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.93  1.08

``` r
map(listcol_df$samp, mean_and_sd)
```

    ## $a
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.05 0.984
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 0.690  9.30
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  19.8 0.910
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.93  1.08

``` r
# add a mean and sd column 
listcol_df |>
  mutate(mean_sd = map(samp, mean_and_sd),
         median = map(samp, median)) |>
  pull(mean_sd)
```

    ## $a
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.05 0.984
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 0.690  9.30
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  19.8 0.910
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.93  1.08

``` r
listcol_df |>
  mutate(mean_sd = map(samp, mean_and_sd),
         median = map(samp, median)) |>
  select(mean_sd, name)
```

    ## # A tibble: 4 × 2
    ##   mean_sd          name 
    ##   <named list>     <chr>
    ## 1 <tibble [1 × 2]> a    
    ## 2 <tibble [1 × 2]> b    
    ## 3 <tibble [1 × 2]> c    
    ## 4 <tibble [1 × 2]> d

## NSDUH
