
Create a map of Australia and Map Data Collection Points
--------------------------------------------------------

This is a simple RMD file to illustrate how to use [*rnaturalearth*](https://github.com/ropenscilabs/rnaturalearth), [*simple features*](https://cran.r-project.org/web/packages/sf/vignettes/sf1.html) and *ggplot2* to create a map of Australia and plot data collection points on it.

### Setup

To do this you'll need a few packages from CRAN:

``` r
#devtools::install_github("ropenscilabs/rnaturalearthdata")
#devtools::install_github("tidyverse/ggplot2")

library(tidyverse)
```

    ## Loading tidyverse: ggplot2
    ## Loading tidyverse: tibble
    ## Loading tidyverse: tidyr
    ## Loading tidyverse: readr
    ## Loading tidyverse: purrr
    ## Loading tidyverse: dplyr

    ## Conflicts with tidy packages ----------------------------------------------

    ## filter(): dplyr, stats
    ## lag():    dplyr, stats

``` r
library(rnaturalearth)
library(raster)
```

    ## Loading required package: sp

    ## 
    ## Attaching package: 'raster'

    ## The following object is masked from 'package:dplyr':
    ## 
    ##     select

    ## The following object is masked from 'package:tidyr':
    ## 
    ##     extract

``` r
library(sf)
```

    ## Linking to GEOS 3.6.2, GDAL 2.2.1, proj.4 4.9.3, lwgeom 2.3.2 r15302

Get the Data to Create Our Map
------------------------------

### Add a Shapefile of Australia

This is our base layer, Australia, of the map from (Naturalearth.com)\[<https://naturalearth.com/>\]

``` r
oz_shape <- rnaturalearth::ne_states(geounit = "australia",
                                     returnclass = "sf")

plot(oz_shape)
```

    ## Warning: plotting the first 9 out of 59 attributes; use max.plot = 59 to
    ## plot all

![](README_files/figure-markdown_github-ascii_identifiers/australia-1.png)

However, it includes several islands and ocean that are not of interest to us. To fix this, crop it down to just the mainland plus Tasmania and remove Jervis Bay so that the labels on the final map product are cleaner.

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

    ## Warning: attribute variables are assumed to be spatially constant
    ## throughout all geometries

``` r
oz_shape <- oz_shape[oz_shape$abbrev != "J.B.T.", ]

plot(oz_shape)
```

    ## Warning: plotting the first 9 out of 59 attributes; use max.plot = 59 to
    ## plot all

![](README_files/figure-markdown_github-ascii_identifiers/crop_shape-1.png)

Import Collection Point Data
----------------------------

Assuming the "Geocode\_319 samples.csv" file is in the same working directory as this script.

``` r
point_data <- read_csv("Geocode_319 samples.csv")
point_data$State <- as.factor(point_data$State)
```

Plot using `ggplot2`
--------------------

Plot the final combination of data with the Naturalearth data Australia and state outlines with the sampling location points overplayed and colour-coded by the state where they were sampled.

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
  geom_point(data = point_data,
  aes(x = Longitude,
  y = Latitude,
  colour = State),
  alpha = 0.6,
  size = 2) +
  theme_bw() +
  xlab("Longitude") +
  ylab("Latitude") +
  theme(legend.position = "none") +
  coord_sf()

oz
```

Save the graph
--------------

Export at 500 dpi for publication with a width 190mm for a 2-column width in an Elsevier publication.

``` r
ggsave("Australia_Map.tiff", width = 190, units = "mm", dpi = 500)
```

Appendix
========

    ## Session info -------------------------------------------------------------

    ##  setting  value                       
    ##  version  R version 3.4.1 (2017-06-30)
    ##  system   x86_64, darwin16.7.0        
    ##  ui       unknown                     
    ##  language (EN)                        
    ##  collate  en_AU.UTF-8                 
    ##  tz       Australia/Brisbane          
    ##  date     2017-09-18

    ## Packages -----------------------------------------------------------------

    ##  package            * version    date      
    ##  assertthat           0.2.0      2017-04-11
    ##  backports            1.1.0      2017-05-22
    ##  base               * 3.4.1      2017-09-03
    ##  bindr                0.1        2016-11-13
    ##  bindrcpp             0.2        2017-06-17
    ##  broom                0.4.2      2017-02-13
    ##  cellranger           1.1.0      2016-07-27
    ##  colorspace           1.3-2      2016-12-14
    ##  compiler             3.4.1      2017-09-03
    ##  datasets           * 3.4.1      2017-09-03
    ##  DBI                  0.7        2017-06-18
    ##  devtools             1.13.3     2017-08-02
    ##  digest               0.6.12     2017-01-27
    ##  dplyr              * 0.7.3      2017-09-09
    ##  evaluate             0.10.1     2017-06-24
    ##  forcats              0.2.0      2017-01-23
    ##  foreign              0.8-69     2017-06-22
    ##  ggplot2            * 2.2.1      2016-12-30
    ##  glue                 1.1.1      2017-06-21
    ##  graphics           * 3.4.1      2017-09-03
    ##  grDevices          * 3.4.1      2017-09-03
    ##  grid                 3.4.1      2017-09-03
    ##  gtable               0.2.0      2016-02-26
    ##  haven                1.1.0      2017-07-09
    ##  hms                  0.3        2016-11-22
    ##  htmltools            0.3.6      2017-04-28
    ##  httr                 1.3.1      2017-08-20
    ##  jsonlite             1.5        2017-06-01
    ##  knitr                1.17       2017-08-10
    ##  lattice              0.20-35    2017-03-25
    ##  lazyeval             0.2.0      2016-06-12
    ##  lubridate            1.6.0      2016-09-13
    ##  magrittr             1.5        2014-11-22
    ##  memoise              1.1.0      2017-04-21
    ##  methods            * 3.4.1      2017-09-03
    ##  mnormt               1.5-5      2016-10-15
    ##  modelr               0.1.1      2017-07-24
    ##  munsell              0.4.3      2016-02-13
    ##  nlme                 3.1-131    2017-02-06
    ##  parallel             3.4.1      2017-09-03
    ##  pkgconfig            2.0.1      2017-03-21
    ##  plyr                 1.8.4      2016-06-08
    ##  psych                1.7.8      2017-09-09
    ##  purrr              * 0.2.3      2017-08-02
    ##  R6                   2.2.2      2017-06-17
    ##  raster             * 2.5-8      2016-06-02
    ##  Rcpp                 0.12.12    2017-07-15
    ##  readr              * 1.1.1      2017-05-16
    ##  readxl               1.0.0      2017-04-18
    ##  reshape2             1.4.2      2016-10-22
    ##  rgeos                0.3-23     2017-04-06
    ##  rlang                0.1.2.9000 2017-09-14
    ##  rmarkdown            1.6        2017-06-15
    ##  rnaturalearth      * 0.1.0      2017-03-21
    ##  rnaturalearthhires   0.1.0      2017-09-18
    ##  rprojroot            1.2        2017-01-16
    ##  rvest                0.3.2      2016-06-17
    ##  scales               0.5.0      2017-08-24
    ##  sf                 * 0.5-4      2017-08-28
    ##  sp                 * 1.2-5      2017-06-29
    ##  stats              * 3.4.1      2017-09-03
    ##  stringi              1.1.5      2017-04-07
    ##  stringr              1.2.0      2017-02-18
    ##  tibble             * 1.3.4      2017-08-22
    ##  tidyr              * 0.7.1      2017-09-01
    ##  tidyverse          * 1.1.1      2017-01-27
    ##  tools                3.4.1      2017-09-03
    ##  udunits2             0.13       2016-11-17
    ##  units                0.4-6      2017-08-27
    ##  utils              * 3.4.1      2017-09-03
    ##  withr                2.0.0      2017-07-28
    ##  xml2                 1.1.1      2017-01-24
    ##  yaml                 2.1.14     2016-11-12
    ##  source                          
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  local                           
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  local                           
    ##  local                           
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  cran (@0.7.3)                   
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  local                           
    ##  local                           
    ##  local                           
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  cran (@1.3.1)                   
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  local                           
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  local                           
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  cran (@1.7.8)                   
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  Github (tidyverse/rlang@ff02f2a)
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  local                           
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  cran (@0.5.0)                   
    ##  cran (@0.5-4)                   
    ##  CRAN (R 3.4.1)                  
    ##  local                           
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  cran (@1.3.4)                   
    ##  cran (@0.7.1)                   
    ##  CRAN (R 3.4.1)                  
    ##  local                           
    ##  CRAN (R 3.4.1)                  
    ##  cran (@0.4-6)                   
    ##  local                           
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)                  
    ##  CRAN (R 3.4.1)
