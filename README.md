
Creating a Map of Australia, Adding Data for Sample Collection Points
---------------------------------------------------------------------

[![render
readme](https://github.com/adamhsparks/R_map_with_points_Oz/workflows/render%20readme/badge.svg)](https://github.com/adamhsparks/R_map_with_points_Oz/actions?query=workflow%3A%22render+readme%22)

This is a simple Rmd file to illustrate how to use
[*rnaturalearth*](https://github.com/ropenscilabs/rnaturalearth),
[*simple
features*](https://cran.r-project.org/web/packages/sf/vignettes/sf1.html)
and [*ggplot2*](https://CRAN.R-project.org/package=ggplot2) to create a
map of Australia and plot data collection points on it.

### Setup

To do this you’ll need a few packages from CRAN:

    if (!require("pacman"))
      install.packages("pacman")

    ## Loading required package: pacman

    pacman::p_load(readr,
                   ggplot2,
                   rnaturalearth,
                   rnaturalearthdata,
                   sf,
                   rgeos,
                   lwgeom
    )

And one from the rOpenSci Labs.

    if (!require("rnaturalearthhires"))
      pacman::p_install_gh("ropenscilabs/rnaturalearthhires")

    ## Loading required package: rnaturalearthhires

### Add a Shapefile of Australia

This is our base layer, Australia, of the map from
(Naturalearth.com)\[<a href="https://naturalearth.com/" class="uri">https://naturalearth.com/</a>\].

    oz_sf <- rnaturalearth::ne_states(geounit = "australia",
                                      returnclass = "sf")

    plot(oz_sf)

    ## Warning: plotting the first 9 out of 83 attributes; use max.plot = 83 to plot
    ## all

![](README_files/figure-gfm/australia-1.png)<!-- -->

However, it includes several islands and ocean that are not of interest
to us. To fix this, crop it down to just the mainland plus Tasmania and
remove Jervis Bay so that the labels on the final map product are
cleaner.

    oz_sf <- st_crop(x = oz_sf,
                     y = c(xmin = 114,
                           xmax =155,
                           ymin = -45,
                           ymax = -9))

    ## although coordinates are longitude/latitude, st_intersection assumes that they are planar

    ## Warning: attribute variables are assumed to be spatially constant throughout all
    ## geometries

    oz_sf <- oz_sf[oz_sf$abbrev != "J.B.T.",]

    plot(oz_sf)

    ## Warning: plotting the first 9 out of 83 attributes; use max.plot = 83 to plot
    ## all

![](README_files/figure-gfm/crop_sf-1.png)<!-- -->

Import Collection Point Data
----------------------------

If you have your own data in a .csv file, import it using `read_csv()`.
These are just random points that I generated and saved as a .csv file
for this work to illustrate how to add points to a map.

    point_data <- read_csv("sample_points.csv",
                           col_types = cols(
                             col_double(),
                             col_double(),
                             col_factor()
                           ))

Plot Using ggplot2
------------------

Plot the final combination of data with the Naturalearthdata Australia
data and state outlines with the sampling location points overplayed and
colour-coded by the state where they were sampled.

    oz <- ggplot(oz_sf) +
      geom_sf(fill = "white") +
      geom_text(
        data = oz_sf,
        aes(x = longitude,
            y = latitude,
            label = abbrev),
        size = 2.5,
        hjust = 1
      ) +
      geom_point(data = point_data,
                 aes(x = Longitude,
                     y = Latitude,
                     colour = class),
                 size = 2) +
      theme_bw() +
      scale_colour_brewer(type = "Qualitative", palette = "Set1") +
      xlab("Longitude") +
      ylab("Latitude") +
      coord_sf()

    oz

![](README_files/figure-gfm/plot-1.png)<!-- -->

Save the Graph
--------------

Export at 500 DPI for publication with a width 190mm for a 2-column
width figure.

    ggsave("Australia_Map.tiff",
           width = 190,
           units = "mm",
           dpi = 500)

Meta
----

### Code of Conduct

Please note that the R\_map\_with\_points\_Oz project is released with a
[Contributor Code of
Conduct](https://contributor-covenant.org/version/2/0/CODE_OF_CONDUCT.html).
By contributing to this project, you agree to abide by its terms.

### R Session Information

    ## R version 4.0.2 (2020-06-22)
    ## Platform: x86_64-apple-darwin17.0 (64-bit)
    ## Running under: macOS Catalina 10.15.6
    ## 
    ## Matrix products: default
    ## BLAS:   /Library/Frameworks/R.framework/Versions/4.0/Resources/lib/libRblas.dylib
    ## LAPACK: /Library/Frameworks/R.framework/Versions/4.0/Resources/lib/libRlapack.dylib
    ## 
    ## locale:
    ## [1] en_AU.UTF-8/en_AU.UTF-8/en_AU.UTF-8/C/en_AU.UTF-8/en_AU.UTF-8
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ##  [1] rnaturalearthhires_0.2.0 lwgeom_0.2-5             rgeos_0.5-3             
    ##  [4] sp_1.4-2                 sf_0.9-5                 rnaturalearthdata_0.1.0 
    ##  [7] rnaturalearth_0.1.0      ggplot2_3.3.2            readr_1.3.1             
    ## [10] pacman_0.5.1            
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] Rcpp_1.0.5         RColorBrewer_1.1-2 pillar_1.4.6       compiler_4.0.2    
    ##  [5] class_7.3-17       tools_4.0.2        digest_0.6.25      lattice_0.20-41   
    ##  [9] evaluate_0.14      lifecycle_0.2.0    tibble_3.0.3       gtable_0.3.0      
    ## [13] pkgconfig_2.0.3    rlang_0.4.7        DBI_1.1.0          yaml_2.2.1        
    ## [17] parallel_4.0.2     xfun_0.16          e1071_1.7-3        withr_2.2.0       
    ## [21] stringr_1.4.0      dplyr_1.0.1        knitr_1.29         generics_0.0.2    
    ## [25] vctrs_0.3.2        hms_0.5.3          classInt_0.4-3     grid_4.0.2        
    ## [29] tidyselect_1.1.0   glue_1.4.1         R6_2.4.1           rmarkdown_2.3     
    ## [33] farver_2.0.3       purrr_0.3.4        magrittr_1.5       units_0.6-7       
    ## [37] scales_1.1.1       ellipsis_0.3.1     htmltools_0.5.0    colorspace_1.4-1  
    ## [41] KernSmooth_2.23-17 stringi_1.4.6      munsell_0.5.0      crayon_1.3.4
