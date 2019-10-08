Tidy Data
================
Katie
10/7/2019

## Wide to long

``` r
pulse_data = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m, 
    names_to = "visit", 
    names_prefix = "bdi_score_", 
    values_to = "bdi"
  ) %>% 
  mutate(
    visit = recode(visit, "bl" = "00m")
  )
```

## Separate in litters

``` r
litters_data = 
  read_csv("./data/FAS_litters.csv") %>% 
  janitor::clean_names() %>% 
  separate(col = group, into = c("dose", "day_of_tx"), 3) %>% 
  mutate(
    dose = str_to_lower(dose), 
    wt_gain = gd18_weight - gd0_weight
  )
```

    ## Parsed with column specification:
    ## cols(
    ##   Group = col_character(),
    ##   `Litter Number` = col_character(),
    ##   `GD0 weight` = col_double(),
    ##   `GD18 weight` = col_double(),
    ##   `GD of Birth` = col_double(),
    ##   `Pups born alive` = col_double(),
    ##   `Pups dead @ birth` = col_double(),
    ##   `Pups survive` = col_double()
    ## )

## Go untidy…

``` r
analysis_result = tibble(
  group = c("treatment", "treatment", "placebo", "placebo"),
  time = c("pre", "post", "pre", "post"),
  mean = c(4, 8, 3.5, 4)
)

pivot_wider(
  analysis_result, 
  names_from = time, 
  values_from = mean
)
```

    ## # A tibble: 2 x 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4