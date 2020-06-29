
## Create a map of Australia and Map Data Collection Points

This is a simple RMD file to illustrate how to use
[*rnaturalearth*](https://github.com/ropenscilabs/rnaturalearth),
[*simple
features*](https://cran.r-project.org/web/packages/sf/vignettes/sf1.html)
and *ggplot2* to create a map of Australia and plot data collection
points on it.

### Setup

To do this you’ll need a few packages from CRAN:

``` r
if (!require("pacman")) {
  install.packages("pacman")
}
```

    ## Loading required package: pacman

``` r
library("pacman")
p_load(tidyverse, rnaturalearth, raster, sf, lwgeom)
if (!require("rnaturalearthdata")) {
  p_install_gh("ropenscilabs/rnaturalearthdata")
}
```

    ## Loading required package: rnaturalearthdata

### Add a Shapefile of Australia

This is our base layer, Australia, of the map from
(Naturalearth.com)\[<https://naturalearth.com/>\]

``` r
oz_shape <- rnaturalearth::ne_states(geounit = "australia",
                                     returnclass = "sf")

plot(oz_shape)
```

    ## Warning: plotting the first 9 out of 83 attributes; use max.plot = 83 to plot
    ## all

![](README_files/figure-gfm/australia-1.png)<!-- -->

However, it includes several islands and ocean that are not of interest
to us. To fix this, crop it down to just the mainland plus Tasmania and
remove Jervis Bay so that the labels on the final map product are
cleaner.

``` r
oz_shape <- st_intersection(oz_shape,
                st_set_crs(
                  st_as_sf(
                    as(
                      raster::extent(114,
                                     155,
                                     -45,
                                     -9),
                      "SpatialPolygons")),
                  st_crs(oz_shape)))
```

    ## although coordinates are longitude/latitude, st_intersection assumes that they are planar

    ## Warning: attribute variables are assumed to be spatially constant throughout all
    ## geometries

``` r
oz_shape <- oz_shape[oz_shape$abbrev != "J.B.T.", ]

plot(oz_shape)
```

    ## Warning: plotting the first 9 out of 83 attributes; use max.plot = 83 to plot
    ## all

![](README_files/figure-gfm/crop_shape-1.png)<!-- -->

## Import Collection Point Data

If you have your own data in a .csv file, import it using `read_csv()`.
These are just random points that I generated to illustrate how to add
points to a map.

``` r
point_data <- read_csv("sample_points.csv",
                       col_types = cols(
                         col_double(),
                         col_double(),
                         col_factor()
                       ))
```

## Plot using ggplot2

Plot the final combination of data with the Naturalearthdata Australia
data and state outlines with the sampling location points overplayed and
colour-coded by the state where they were sampled.

``` r
oz <- ggplot(oz_shape) +
  geom_sf(fill = "white") +
  geom_text(
    data = oz_shape,
    aes(x = longitude,
        y = latitude,
        label = abbrev),
    size = 2.5,
    hjust = 1
  ) +
  geom_point(
    data = point_data,
    aes(x = Longitude,
        y = Latitude,
        colour = class),
    size = 2
  ) +
  theme_bw() +
  scale_colour_brewer(type = "Qualitative", palette = "Set1") +
  xlab("Longitude") +
  ylab("Latitude") +
  coord_sf()

oz
```

![](README_files/figure-gfm/plot-1.png)<!-- -->

## Save the graph

Export at 500 DPI for publication with a width 190mm for a 2-column
width figure.

``` r
ggsave("Australia_Map.tiff",
       width = 190,
       units = "mm",
       dpi = 500)
```

# Appendix

    ## ─ Session info ───────────────────────────────────────────────────────────────
    ##  setting  value                       
    ##  version  R version 4.0.2 (2020-06-22)
    ##  os       macOS Catalina 10.15.5      
    ##  system   x86_64, darwin17.0          
    ##  ui       X11                         
    ##  language (EN)                        
    ##  collate  en_AU.UTF-8                 
    ##  ctype    en_AU.UTF-8                 
    ##  tz       Australia/Brisbane          
    ##  date     2020-06-29                  
    ## 
    ## ─ Packages ───────────────────────────────────────────────────────────────────
    ##  package            * version    date       lib
    ##  assertthat           0.2.1      2019-03-21 [1]
    ##  backports            1.1.8      2020-06-17 [1]
    ##  blob                 1.2.1      2020-01-20 [1]
    ##  broom                0.5.6      2020-04-20 [1]
    ##  callr                3.4.3      2020-03-28 [1]
    ##  cellranger           1.1.0      2016-07-27 [1]
    ##  class                7.3-17     2020-04-26 [2]
    ##  classInt             0.4-3      2020-04-07 [1]
    ##  cli                  2.0.2      2020-02-28 [1]
    ##  clisymbols           1.2.0      2017-05-21 [1]
    ##  codetools            0.2-16     2018-12-24 [2]
    ##  colorspace           1.4-1      2019-03-18 [1]
    ##  crayon               1.3.4.9000 2020-06-12 [1]
    ##  DBI                  1.1.0      2019-12-15 [1]
    ##  dbplyr               1.4.4      2020-05-27 [1]
    ##  desc                 1.2.0      2018-05-01 [1]
    ##  devtools             2.3.0      2020-04-10 [1]
    ##  digest               0.6.25     2020-02-23 [1]
    ##  dplyr              * 1.0.0      2020-05-29 [1]
    ##  e1071                1.7-3      2019-11-26 [1]
    ##  ellipsis             0.3.1      2020-05-15 [1]
    ##  evaluate             0.14       2019-05-28 [1]
    ##  fansi                0.4.1      2020-01-08 [1]
    ##  farver               2.0.3      2020-01-16 [1]
    ##  forcats            * 0.5.0      2020-03-01 [1]
    ##  fs                   1.4.1      2020-04-04 [1]
    ##  generics             0.0.2      2018-11-29 [1]
    ##  ggplot2            * 3.3.2      2020-06-19 [1]
    ##  glue                 1.4.1      2020-05-13 [1]
    ##  gtable               0.3.0      2019-03-25 [1]
    ##  haven                2.3.1      2020-06-01 [1]
    ##  hms                  0.5.3      2020-01-08 [1]
    ##  htmltools            0.5.0      2020-06-16 [1]
    ##  httr                 1.4.1      2019-08-05 [1]
    ##  jsonlite             1.7.0      2020-06-25 [1]
    ##  KernSmooth           2.23-17    2020-04-26 [2]
    ##  knitr                1.29       2020-06-23 [1]
    ##  lattice              0.20-41    2020-04-02 [2]
    ##  lifecycle            0.2.0      2020-03-06 [1]
    ##  lubridate            1.7.9      2020-06-08 [1]
    ##  lwgeom             * 0.2-5      2020-06-12 [1]
    ##  magrittr             1.5        2014-11-22 [1]
    ##  memoise              1.1.0      2017-04-21 [1]
    ##  modelr               0.1.8      2020-05-19 [1]
    ##  munsell              0.5.0      2018-06-12 [1]
    ##  nlme                 3.1-148    2020-05-24 [2]
    ##  pacman             * 0.5.1      2019-03-11 [1]
    ##  pillar               1.4.4      2020-05-05 [1]
    ##  pkgbuild             1.0.8      2020-05-07 [1]
    ##  pkgconfig            2.0.3      2019-09-22 [1]
    ##  pkgload              1.1.0      2020-05-29 [1]
    ##  prettyunits          1.1.1      2020-01-24 [1]
    ##  processx             3.4.2      2020-02-09 [1]
    ##  prompt               1.0.0      2020-04-25 [1]
    ##  ps                   1.3.3      2020-05-08 [1]
    ##  purrr              * 0.3.4      2020-04-17 [1]
    ##  R6                   2.4.1      2019-11-12 [1]
    ##  raster             * 3.3-7      2020-06-27 [1]
    ##  RColorBrewer         1.1-2      2014-12-07 [1]
    ##  Rcpp                 1.0.4.6    2020-04-09 [1]
    ##  readr              * 1.3.1      2018-12-21 [1]
    ##  readxl               1.3.1      2019-03-13 [1]
    ##  remotes              2.1.1      2020-02-15 [1]
    ##  reprex               0.3.0      2019-05-16 [1]
    ##  rgeos                0.5-3      2020-05-08 [1]
    ##  rlang                0.4.6      2020-05-02 [1]
    ##  rmarkdown            2.3        2020-06-18 [1]
    ##  rnaturalearth      * 0.1.0      2017-03-21 [1]
    ##  rnaturalearthdata  * 0.1.0      2017-02-21 [1]
    ##  rnaturalearthhires   0.2.0      2020-06-29 [1]
    ##  rprojroot            1.3-2      2018-01-03 [1]
    ##  rstudioapi           0.11       2020-02-07 [1]
    ##  rvest                0.3.5      2019-11-08 [1]
    ##  scales               1.1.1      2020-05-11 [1]
    ##  sessioninfo          1.1.1      2018-11-05 [1]
    ##  sf                 * 0.9-4      2020-06-13 [1]
    ##  sp                 * 1.4-2      2020-05-20 [1]
    ##  stringi              1.4.6      2020-02-17 [1]
    ##  stringr            * 1.4.0      2019-02-10 [1]
    ##  testthat             2.3.2      2020-03-02 [1]
    ##  tibble             * 3.0.1      2020-04-20 [1]
    ##  tidyr              * 1.1.0      2020-05-20 [1]
    ##  tidyselect           1.1.0      2020-05-11 [1]
    ##  tidyverse          * 1.3.0      2019-11-21 [1]
    ##  units                0.6-7      2020-06-13 [1]
    ##  usethis              1.6.1      2020-04-29 [1]
    ##  vctrs                0.3.1      2020-06-05 [1]
    ##  withr                2.2.0      2020-04-20 [1]
    ##  xfun                 0.15       2020-06-21 [1]
    ##  xml2                 1.3.2      2020-04-23 [1]
    ##  yaml                 2.2.1      2020-02-01 [1]
    ##  source                                      
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.1)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.2)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.2)                              
    ##  CRAN (R 4.0.0)                              
    ##  Github (r-lib/crayon@dcf6d44)               
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.1)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.1)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.2)                              
    ##  CRAN (R 4.0.2)                              
    ##  CRAN (R 4.0.1)                              
    ##  CRAN (R 4.0.2)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.2)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  Github (gaborcsardi/prompt@b332c42)         
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.2)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.2)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.1)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  Github (ropensci/rnaturalearthhires@2ed7a93)
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.1)                              
    ##  CRAN (R 4.0.0)                              
    ##  CRAN (R 4.0.0)                              
    ## 
    ## [1] /Users/adamsparks/.R/library
    ## [2] /Library/Frameworks/R.framework/Versions/4.0/Resources/library
