tidy data
================
Yan Li
2024-10-01

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

# pivot_longer

``` r
pulse_df = 
  haven::read_sas("data/public_pulse_data.sas7bdat") |>
  janitor::clean_names()
pulse_df
```

    ## # A tibble: 1,087 × 7
    ##       id   age sex    bdi_score_bl bdi_score_01m bdi_score_06m bdi_score_12m
    ##    <dbl> <dbl> <chr>         <dbl>         <dbl>         <dbl>         <dbl>
    ##  1 10003  48.0 male              7             1             2             0
    ##  2 10015  72.5 male              6            NA            NA            NA
    ##  3 10022  58.5 male             14             3             8            NA
    ##  4 10026  72.7 male             20             6            18            16
    ##  5 10035  60.4 male              4             0             1             2
    ##  6 10050  84.7 male              2            10            12             8
    ##  7 10078  31.3 male              4             0            NA            NA
    ##  8 10088  56.9 male              5            NA             0             2
    ##  9 10091  76.0 male              0             3             4             0
    ## 10 10092  74.2 female           10             2            11             6
    ## # ℹ 1,077 more rows

- the BDI score is spread across four columns, which correspond to four
  observation times. We can fix this problem using pivot_longer:

``` r
pulse_tidy_df = 
  pivot_longer(
    pulse_df, 
    bdi_score_bl:bdi_score_12m,
    names_to = "visit", 
    values_to = "bdi")

pulse_tidy_df
```

    ## # A tibble: 4,348 × 5
    ##       id   age sex   visit           bdi
    ##    <dbl> <dbl> <chr> <chr>         <dbl>
    ##  1 10003  48.0 male  bdi_score_bl      7
    ##  2 10003  48.0 male  bdi_score_01m     1
    ##  3 10003  48.0 male  bdi_score_06m     2
    ##  4 10003  48.0 male  bdi_score_12m     0
    ##  5 10015  72.5 male  bdi_score_bl      6
    ##  6 10015  72.5 male  bdi_score_01m    NA
    ##  7 10015  72.5 male  bdi_score_06m    NA
    ##  8 10015  72.5 male  bdi_score_12m    NA
    ##  9 10022  58.5 male  bdi_score_bl     14
    ## 10 10022  58.5 male  bdi_score_01m     3
    ## # ℹ 4,338 more rows

- now visit is an issue. The original column names were informative but
  we probably don’t need to keep the bdi_score\_ prefix in each case.
  I’ll use an additional option in pivot_longer to address this:

``` r
pulse_tidy_df = 
  pivot_longer(
    pulse_df, 
    bdi_score_bl:bdi_score_12m,
    names_to = "visit", 
    names_prefix = "bdi_score_",
    values_to = "bdi")

pulse_tidy_df
```

    ## # A tibble: 4,348 × 5
    ##       id   age sex   visit   bdi
    ##    <dbl> <dbl> <chr> <chr> <dbl>
    ##  1 10003  48.0 male  bl        7
    ##  2 10003  48.0 male  01m       1
    ##  3 10003  48.0 male  06m       2
    ##  4 10003  48.0 male  12m       0
    ##  5 10015  72.5 male  bl        6
    ##  6 10015  72.5 male  01m      NA
    ##  7 10015  72.5 male  06m      NA
    ##  8 10015  72.5 male  12m      NA
    ##  9 10022  58.5 male  bl       14
    ## 10 10022  58.5 male  01m       3
    ## # ℹ 4,338 more rows
