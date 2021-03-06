
<!-- README.md is generated from README.Rmd. Please edit that file -->

# ggbump

<!-- badges: start -->

<!-- badges: end -->

The R package `ggbump` creates elegant bump charts in ggplot.Bump charts
are good to use to plot rankning over time.

## Installation

You can install the released version of ggbump from
[github](https://github.com/davidsjoberg/ggbump) with:

``` r
devtools::install_github("davidsjoberg/ggbump")
```

## Bump chart Examples

Basic example:

<img src="man/figures/README-main_plot-1.png" width="100%" />

A more advanced example:

![Example2](https://user-images.githubusercontent.com/44140737/75692519-fb146b00-5ca5-11ea-85f5-9fc33e760a7d.png)

[Click here for code to the plot
above](https://github.com/davidsjoberg/ggbump/wiki/My-year-on-Spotify)

Flags could be used instead of names:

![Example3](https://user-images.githubusercontent.com/44140737/76630573-8f04f300-6540-11ea-802a-653e3b509dfa.png)

[Click here for code to the plot
above](https://github.com/davidsjoberg/ggbump/wiki/geom_bump-with-flags)

# Tutorial

## Prep

Load packages and get som data with rank:

``` r
if(!require(pacman)) install.packages("pacman")
library(ggbump)
pacman::p_load(tidyverse, cowplot, wesanderson)

df <- tibble(country = c("India", "India", "India", "Sweden", "Sweden", "Sweden", "Germany", "Germany", "Germany", "Finland", "Finland", "Finland"),
             year = c(2011, 2012, 2013, 2011, 2012, 2013, 2011, 2012, 2013, 2011, 2012, 2013),
             rank = c(4, 2, 2, 3, 1, 4, 2, 3, 1, 1, 4, 3))

knitr::kable(head(df))
```

| country | year | rank |
| :------ | ---: | ---: |
| India   | 2011 |    4 |
| India   | 2012 |    2 |
| India   | 2013 |    2 |
| Sweden  | 2011 |    3 |
| Sweden  | 2012 |    1 |
| Sweden  | 2013 |    4 |

## Make a bump chart

Most simple use case:

``` r
ggplot(df, aes(year, rank, color = country)) +
    geom_bump()
```

<img src="man/figures/README-pressure-1.png" width="100%" />

## Pimp the bump chart\!

Improve the bump chart by adding:

  - A point for each rank observation.
  - Choose a minimal theme, I use `theme_minimal_grid()` from `cowplot`.
  - Choose nice colors so it does not look generic ggplot. I use a
    palette from `wesanderson`.
  - Remove legend and add labels at the start and end of the bumpy ride.
  - Reverse the y-axis to get rank 1 at the top.
  - Adjust the ‘smoothness’ of the lines by setting `smooth` to 8.
    Higher means less smooth.

<!-- end list -->

``` r

ggplot(df, aes(year, rank, color = country)) +
  geom_point(size = 7) +
  geom_text(data = df %>% filter(year == min(year)),
            aes(x = year - .1, label = country), size = 5, hjust = 1) +
  geom_text(data = df %>% filter(year == max(year)),
            aes(x = year + .1, label = country), size = 5, hjust = 0) +
  geom_bump(size = 2, smooth = 8) +
  scale_x_continuous(limits = c(2010.6, 2013.4),
                     breaks = seq(2011, 2013, 1)) +
  theme_minimal_grid(font_size = 14, line_size = 0) +
  theme(legend.position = "none",
        panel.grid.major = element_blank()) +
  labs(y = "RANK",
       x = NULL) +
  scale_y_reverse() +
  scale_color_manual(values = wes_palette(n = 4, name = "GrandBudapest1"))
#> Warning: Ignoring unknown parameters: smooth
```

<img src="man/figures/README-unnamed-chunk-2-1.png" width="100%" />

## geom\_bump with factors

To use `geom_bump` with factors or character axis you need to prepare
the data frame before. You need to prepare one column for the numeric
position and one column with the name. If you want to have
character/factor on both y and x you need to prepare 4 columns.

``` r
# Original df
df <- tibble(season = c("Spring", "Summer", "Autumn", "Winter", 
                        "Spring", "Summer", "Autumn", "Winter", 
                        "Spring", "Summer", "Autumn", "Winter"),
             position = c("Gold", "Gold", "Bronze", "Gold",
                          "Silver", "Bronze", "Gold", "Silver",
                          "Bronze", "Silver", "Silver", "Bronze"),
             player = c(rep("David", 4),
                        rep("Anna", 4),
                        rep("Franz", 4)))

# Create factors and numeric columns
df <- df %>% 
  mutate(season = factor(season, 
                         levels = c("Spring", "Summer", "Autumn", "Winter")),
         x = as.numeric(season),
         position = factor(position, 
                           levels = c("Gold", "Silver", "Bronze")),
         y = as.numeric(position))

# Add manual axis labels to plot
p <- ggplot(df, aes(x, y, color = player)) +
  geom_bump(size = 2, smooth = 8, show.legend = F) +
  geom_point(size = 5, aes(shape = player)) +
  scale_x_continuous(breaks = df$x %>% unique(),
                     labels = df$season %>% levels()) +
  scale_y_reverse(breaks = df$y %>% unique(),
                     labels = df$position %>% levels())
#> Warning: Ignoring unknown parameters: smooth
p
```

<img src="man/figures/README-unnamed-chunk-3-1.png" width="100%" />

### And some nice theme features

``` r
p +
  theme_minimal_grid(font_size = 14, line_size = 0) +
  theme(panel.grid.major = element_blank(),
        axis.ticks = element_blank()) +
  labs(y = "Medal",
       x = "Season",
       color = NULL,
       shape = NULL) +
  scale_color_manual(values = wes_palette(n = 3, name = "IsleofDogs1"))
```

<img src="man/figures/README-unnamed-chunk-4-1.png" width="100%" />

## Feedback

If you find any error or have suggestions for improvements you ar emore
than welcome to contact me :)
