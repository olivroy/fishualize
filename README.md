-   [Introduction](#introduction)
-   [The color scales](#the-color-scales)
-   [Usage](#usage)

[![Build
Status](https://api.travis-ci.org/nschiett/fishualize.png?branch=master)](https://travis-ci.org/nschiett/fishualize)
\[![GitHub All
Releases](https://img.shields.io/github/downloads/nschiett/fishualize/total)

<img src="man/figures/fishualize_logo.png" width = 120 alt="fishflux logo"/>

Introduction
============

The [**fishualize**](http:://github.com/nschiett/fishualize) package
provides color scales based on the natural colors of fishes.

Installation
------------

``` r
library(devtools)
devtools::install_github("nschiett/fishualize", force = TRUE)
library(fishualize)
```

The color scales
================

The package contains one color scale per species. The amount of color
scales/species will expand over time. To get a list of fish species that
are available, run `fish_palettes()`.  
An overview of the color scales can be found
[here](https://nschiett.github.io/fishualize/articles/overview_colors.html).

Usage
=====

The `fish()` function produces the fish color scale based on your
favourite species, indicated by ‘option’.

For base plots, use the `fish()` function to generate a palette:

``` r
pal <- fish(256, option = "Trimma_lantana")
image(volcano, col = pal)
```

<img src="README_files/figure-markdown_github/tldr_base-1.png" width="672" />

``` r
pal <- fish(10, option = "Ostracion_cubicus")
image(volcano, col = pal)
```

<img src="README_files/figure-markdown_github/tldr_base-2.png" width="672" />

ggplot2
-------

The package also contains color scale functions for **ggplot** plots:
`scale_color_fish()` and `scale_fill_fish()`.

``` r
library(ggplot2)
library(rfishbase)

# load dome data
spp <- fishualize::fish_palettes()
dt <- rfishbase::species(gsub("_"," ", spp))

# plot
ggplot(dt[!is.na(dt$Importance),]) +
  geom_bar(aes(x = Importance, fill = Importance)) +
  scale_fill_fish_d(option = "Scarus_quoyi") +
  theme_bw() +
  theme(axis.text.x= element_blank() )
```

<img src="README_files/figure-markdown_github/tldr_ggplot-1.png" width="672" />

``` r
ggplot(dt) +
  geom_point(aes(x = Length, y = Vulnerability, color = Vulnerability), size = 3) +
  scale_color_fish(option = "Hypsypops_rubicundus", direction = -1) +
  theme_bw()
```

    ## Warning: Removed 1 rows containing missing values (geom_point).

<img src="README_files/figure-markdown_github/tldr_ggplot-2.png" width="672" />

``` r
data <- rfishbase::ecology(gsub("_"," ", spp), c("SpecCode","FeedingType", "DietTroph")) %>% 
  dplyr::left_join( rfishbase::species(gsub("_"," ", spp)))


ggplot(data[!is.na(data$FeedingType),]) +
  geom_boxplot(aes(x = FeedingType, y = log(Length), fill = FeedingType )) +
  scale_fill_fish_d(option = "Cirrhilabrus_solorensis") +
  theme_bw() +
  theme(axis.text.x= element_blank() )
```

<img src="README_files/figure-markdown_github/tldr_ggplot-3.png" width="672" />

``` r
ggplot(data) +
  geom_point(aes(x = Length, y = DietTroph, color = Vulnerability), size = 6, alpha = 0.8) +
  scale_color_fish(option = "Lepomis_megalotis", direction = -1) +
  theme_bw()
```

    ## Warning: Removed 43 rows containing missing values (geom_point).

<img src="README_files/figure-markdown_github/tldr_ggplot-4.png" width="672" />

Here the color scale based on ‘Zebrasoma\_velifer’ and for a cloropleth
map of U.S. unemployment:

``` r
unemp <- read.csv("http://datasets.flowingdata.com/unemployment09.csv",
                  header = FALSE, stringsAsFactors = FALSE)
names(unemp) <- c("id", "state_fips", "county_fips", "name", "year",
                  "?", "?", "?", "rate")
unemp$county <- tolower(gsub(" County, [A-Z]{2}", "", unemp$name))
unemp$county <- gsub("^(.*) parish, ..$","\\1", unemp$county)
unemp$state <- gsub("^.*([A-Z]{2}).*$", "\\1", unemp$name)
county_df <- map_data("county", projection = "albers", parameters = c(39, 45))
names(county_df) <- c("long", "lat", "group", "order", "state_name", "county")
county_df$state <- state.abb[match(county_df$state_name, tolower(state.name))]
county_df$state_name <- NULL
state_df <- map_data("state", projection = "albers", parameters = c(39, 45))
choropleth <- merge(county_df, unemp, by = c("state", "county"))
choropleth <- choropleth[order(choropleth$order), ]

ggplot(choropleth, aes(long, lat, group = group)) +
  geom_polygon(aes(fill = rate), colour = alpha("white", 1 / 2), size = 0.2) +
  geom_polygon(data = state_df, colour = "white", fill = NA) +
  coord_fixed() +
  theme_minimal() +
  ggtitle("US unemployment rate by county") +
  theme(axis.line = element_blank(), axis.text = element_blank(),
        axis.ticks = element_blank(), axis.title = element_blank()) +
  scale_fill_fish(option = "Zebrasoma_velifer")
```

<img src="README_files/figure-markdown_github/ggplot2-1.png" width="672" />

Contribute
----------

Check out how you can contribute to this package
[here](https://nschiett.github.io/fishualize/articles/overview_colors.html)

Credits
-------

Credits for the initial structure of the functions for this package go
to the `harrypotter` package made by Alejandro Jiménez:
<https://github.com/aljrico/harrypotter>
