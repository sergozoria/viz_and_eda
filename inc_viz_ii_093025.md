Viz II - Visualization with ggplot2
================
September 30, 2025

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.2
    ## ✔ ggplot2   3.5.2     ✔ tibble    3.3.0
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ## ✔ purrr     1.1.0     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(patchwork)
library(p8105.datasets)
```

``` r
data("weather_df")
```

Let’s make a basic scatterplot. We can use xlabs and ylabs to label the
axis.Aadd labels if you’re sending to someone else

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name), alpha = 0.5) +
  labs(
      x = "Minimun daily temp",
      y = "Maximun daily temp",
      title = "Temperature scatterplot",
      caption = "Data from NOAA",
      color = "Location"
  )
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](inc_viz_ii_093025_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

We are able to control the scales in the graphs. Scale x continous
because oru x-axis is contrinuous but can also be categotical. We can
labels and breaks. We can do a square root transformaiton on the y axis.
We can also add limits to zoom in the plots

We can also do filter to place limits rather than using the limits
function. This can be done before typing ggplot. For example,
filter(tmin \> 10, \<tmax 30 ).

We can alos playing around with the color pallete using scale_color_hue,
but don’t do that. Refer to color palletes used from online sources.
Look at the p8105 website for reference. Use the viridis package instead
using the viridis::scale_color_viridi

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name), alpha = 0.5) +
  labs(
      x = "Minimun daily temp",
      y = "Maximun daily temp",
      title = "Temperature scatterplot",
      caption = "Data from NOAA",
      color = "Location"
  ) +
  scale_x_continuous(
    breaks = c(-20, 0, 25),
    labels = c("-20C", "0", "25")
  ) +
  scale_y_continuous(
    trans = "sqrt",
    limits = c(10, 30)
  ) +
  viridis::scale_color_viridis(
    discrete = TRUE
  )
```

    ## Warning in transformation$transform(x): NaNs produced

    ## Warning in scale_y_continuous(trans = "sqrt", limits = c(10, 30)): sqrt
    ## transformation introduced infinite values.

    ## Warning: Removed 843 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](inc_viz_ii_093025_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

Themes help with changing everything in the plot.

We can also use ggsave to save the plot or create a variable to save the
plot in the workspace/environment for manipulation letter

``` r
gg_temperature = weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name), alpha = 0.5) +
  labs(
      x = "Minimun daily temp",
      y = "Maximun daily temp",
      title = "Temperature scatterplot",
      caption = "Data from NOAA",
      color = "Location"
  ) +
  scale_x_continuous(
    breaks = c(-20, 0, 25),
    labels = c("-20C", "0", "25")
  ) +
  scale_y_continuous(
    trans = "sqrt",
    limits = c(10, 30)
  ) +
  viridis::scale_color_viridis(
    discrete = TRUE
  )
```

Themes help with changing everything in the plot. You can use
theme_minimal, theme_bw, theme_dark to change the theme of the plot.
legend.posotion moves the legends to the bottom of the X axis.
These_minimal we get white backgrounds with gray lines

``` r
gg_temperature + 
  theme_minimal() +
  theme(legend.position = "bottom")
```

    ## Warning in transformation$transform(x): NaNs produced

    ## Warning in scale_y_continuous(trans = "sqrt", limits = c(10, 30)): sqrt
    ## transformation introduced infinite values.

    ## Warning: Removed 843 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](inc_viz_ii_093025_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Let’s add data in geoms.

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

    ## Warning: Removed 1 row containing missing values or values outside the scale range
    ## (`geom_point()`).

![](inc_viz_ii_093025_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Let’s make three plots and combine using patchwork

``` r
ggp_tmax_tmin =
  weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color= name)) +
  geom_point(alpha = 0.5) +
  theme(legend.position = "bottom")

ggp_prec_density =
  weather_df |> 
  filter(prcp > 0) |> 
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = 0.5) +
  theme(legend.position = "none")

ggp_temp_season =
  weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point(alpha = 0.5) +
  geom_smooth(se = FALSE) +
  theme(legend.position = "bottom")
```

Let’s combine plots side by side using + and / to change positions in
the same place to convey as much information as possible

``` r
(ggp_tmax_tmin + ggp_prec_density) / ggp_temp_season
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).
    ## Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](inc_viz_ii_093025_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

Let’s now do some data manipulation. Factor variables \> rads a string
like centrla park but it reads a number. Factor are in alphabetical
order. But maybe you want things in a particular order. We need to
convince ggplot that we want certain things as a factor. We need to move
things arund using the mutate function. Take a look below. Factor
relevel asings molokai 1, centralpark 2, and waterhole 3

``` r
weather_df |> 
  mutate(name = fct_relevel(name, c("Molokai_HI", "CentralPark_NY", "Waterhole_WA"))) |> 
  ggplot(aes(x = name, y = tmax, fill= name)) +
  geom_violin(alpha = 0.5)
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

![](inc_viz_ii_093025_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

Factor order tells R which variable you’re operating on and which things
you’re reodering it in respect to

``` r
weather_df |> 
  mutate(name = fct_reorder(name, tmax)) |> 
  ggplot(aes(x = name, y = tmax, fill= name)) +
  geom_violin(alpha = 0.5)
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `name = fct_reorder(name, tmax)`.
    ## Caused by warning:
    ## ! `fct_reorder()` removing 17 missing values.
    ## ℹ Use `.na_rm = TRUE` to silence this message.
    ## ℹ Use `.na_rm = FALSE` to preserve NAs.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

![](inc_viz_ii_093025_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Factor in order, basically means what order the data appears in the
dataset

``` r
pulse_df =
  haven::read_sas("./data/public_pulse_data.sas7bdat") |> 
  janitor::clean_names() |> 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
  ) |> 
  mutate(visit = fct_inorder(visit))

pulse_df |> 
  ggplot(aes(x = visit, y = bdi)) +
  geom_boxplot()
```

    ## Warning: Removed 879 rows containing non-finite outside the scale range
    ## (`stat_boxplot()`).

![](inc_viz_ii_093025_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->
