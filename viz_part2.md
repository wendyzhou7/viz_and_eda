Viz part 2
================

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
library(ggridges)
library(patchwork)
```

``` r
knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)
```

Get data for plotting:

``` r
weather_df = 
  rnoaa::meteo_pull_monitors( # pulling 3 monitors
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2022-01-01",
    date_max = "2023-12-31") |>
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USW00022534 = "Molokai_HI",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## using cached file: /Users/wendyzhou/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-10-05 10:33:23.821468 (8.527)

    ## file min/max dates: 1869-01-01 / 2023-10-31

    ## using cached file: /Users/wendyzhou/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2023-10-05 10:33:35.428508 (3.832)

    ## file min/max dates: 1949-10-01 / 2023-10-31

    ## using cached file: /Users/wendyzhou/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-10-05 10:33:39.177801 (0.997)

    ## file min/max dates: 1999-09-01 / 2023-10-31

This results in a dataframe with 2007 observations on 6 variables.

labs

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) + 
  labs(
    title = "Temperature Plot",
    x = "Min daily temp (˚C) ",
    y = "Max daily temp (˚C) ",
    color = "Location",
    caption = "Max vs min daily temp in threee locations; data from r noaa"
  ) + 
  scale_x_continuous(
    breaks = c(-15, 0, 15),
    labels = c("-15 C", "0 C", "15 C")
  ) + 
  scale_y_continuous(
    position = "right",
    trans = "sqrt", #squareoot transformation
    limits = c(0,30) # zooming into this part of the graph
  ) + 
  scale_color_hue(h = c(100, 300))
```

    ## Warning in self$trans$transform(x): NaNs produced

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 402 rows containing missing values (`geom_point()`).

<img src="viz_part2_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />

colors…

``` r
ggp_temp_plot = 
  weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) + 
  labs(
    title = "Temperature plot",
    x = "Minimum daily temperature (C)",
    y = "Maxiumum daily temperature (C)",
    color = "Location",
    caption = "Data from the rnoaa package"
  ) + 
  viridis::scale_color_viridis(name = "Location", discrete = TRUE)

ggp_temp_plot
```

    ## Warning: Removed 105 rows containing missing values (`geom_point()`).

<img src="viz_part2_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

## Themes

``` r
ggp_temp_plot = 
  weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) + 
  labs(
    title = "Temperature plot",
    x = "Minimum daily temperature (C)",
    y = "Maxiumum daily temperature (C)",
    color = "Location",
    caption = "Data from the rnoaa package"
  ) + 
  viridis::scale_color_viridis(name = "Location", discrete = TRUE) + 
  theme(legend.position = "bottom")



ggp_temp_plot
```

    ## Warning: Removed 105 rows containing missing values (`geom_point()`).

<img src="viz_part2_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />

## data argument

``` r
weather_df |> 
  ggplot(aes(x = date, y = tmax)) +
  geom_point(aes(color = name), alpha = .5) + 
  geom_smooth()
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 105 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 105 rows containing missing values (`geom_point()`).

<img src="viz_part2_files/figure-gfm/unnamed-chunk-7-1.png" width="90%" />

``` r
central_park_df = 
  weather_df |> 
  filter(name == "CentralPark_NY")

molokai_df = 
  weather_df |> 
  filter(name == "Molokai_HI")

ggplot(data = molokai_df, aes(x = date, y = tmax, color = name)) + 
  geom_point() + 
  geom_line(data = central_park_df) 
```

    ## Warning: Removed 31 rows containing missing values (`geom_point()`).

    ## Warning: Removed 30 rows containing missing values (`geom_line()`).

<img src="viz_part2_files/figure-gfm/unnamed-chunk-7-2.png" width="90%" />

## ‘patchwork’

``` r
ggp_temp = 
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) +
  theme(legend.position = "none")

ggp_prcp = 
weather_df |> 
  filter(prcp > 25) |> 
  ggplot(aes(x = prcp, fill = name)) + 
  geom_density(alpha = .5) + 
  theme(legend.position = "none")

tmax_date_p = 
  weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point(alpha = .5) +
  geom_smooth(se = FALSE) + 
  theme(legend.position = "bottom")

(ggp_temp + ggp_prcp) / tmax_date_p # displays both graphs next to each other
```

    ## Warning: Removed 105 rows containing missing values (`geom_point()`).

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 105 rows containing non-finite values (`stat_smooth()`).
    ## Removed 105 rows containing missing values (`geom_point()`).

<img src="viz_part2_files/figure-gfm/unnamed-chunk-8-1.png" width="90%" />

## data manipulation

``` r
weather_df |> 
  mutate(
    name = fct_relevel(name, c("Molokai_HI", "CentralPark_NY", "Waterhole_WA")) # put in order of name
  ) |> 
  ggplot(aes(x = name, y = tmax)) + 
  geom_boxplot()
```

    ## Warning: Removed 105 rows containing non-finite values (`stat_boxplot()`).

<img src="viz_part2_files/figure-gfm/unnamed-chunk-9-1.png" width="90%" />

``` r
weather_df |>
  mutate(
    name = fct_reorder(name, tmax) # put in order by increasing tmax
    ) |> 
  ggplot(aes(x = name, y = tmax)) + 
  geom_violin(aes(fill = name), color = "blue", alpha = .5)
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `name = fct_reorder(name, tmax)`.
    ## Caused by warning:
    ## ! `fct_reorder()` removing 105 missing values.
    ## ℹ Use `.na_rm = TRUE` to silence this message.
    ## ℹ Use `.na_rm = FALSE` to preserve NAs.

    ## Warning: Removed 105 rows containing non-finite values (`stat_ydensity()`).

<img src="viz_part2_files/figure-gfm/unnamed-chunk-10-1.png" width="90%" />

## complicated FAS plot

``` r
litter_df = 
  read_csv("./data/FAS_litters.csv") |>
  janitor::clean_names() |>
  separate(group, into = c("dose", "day_of_tx"), sep = 3) # separate 3 characters in
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
pup_df = 
  read_csv("./data/FAS_pups.csv") |>
  janitor::clean_names()
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
fas_data = 
  left_join(pup_df, litter_df, by = "litter_number") 

fas_data |> 
  select(dose, day_of_tx, pd_ears:pd_walk) |> 
  pivot_longer(
    pd_ears:pd_walk,
    names_to = "outcome", 
    values_to = "pn_day") |> 
  drop_na() |> 
  mutate(outcome = fct_reorder(outcome, pn_day, median)) |> 
  ggplot(aes(x = dose, y = pn_day)) + 
  geom_violin() + 
  facet_grid(day_of_tx ~ outcome)
```

<img src="viz_part2_files/figure-gfm/unnamed-chunk-11-1.png" width="90%" />
