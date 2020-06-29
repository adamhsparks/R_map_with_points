Creating a Map of Australia, Adding Data for Sample Collection Points
---------------------------------------------------------------------

This is a simple RMD file to illustrate how to use
[*rnaturalearth*](https://github.com/ropenscilabs/rnaturalearth),
[*simple
features*](https://cran.r-project.org/web/packages/sf/vignettes/sf1.html)
and *ggplot2* to create a map of Australia and plot data collection
points on it.

### Setup

To do this you’ll need a few packages from CRAN:

    if (!require("pacman")) {
      install.packages("pacman")
    }

    ## Loading required package: pacman

    ## Installing package into '/Users/runner/runners/2.263.0/work/_temp/Library'
    ## (as 'lib' is unspecified)

    ## also installing the dependency 'remotes'

    ## 
    ## The downloaded binary packages are in
    ##  /var/folders/24/8k48jl6d249_n_qfxwsl6xvm0000gn/T//RtmpAjXAZs/downloaded_packages

    library("pacman")
    p_load(tidyverse, rnaturalearth, rnaturalearthdata, raster, sf, rgeos, lwgeom)

    ## Installing package into '/Users/runner/runners/2.263.0/work/_temp/Library'
    ## (as 'lib' is unspecified)

    ## also installing the dependencies 'desc', 'pkgbuild', 'rprojroot', 'pkgload', 'praise', 'colorspace', 'sys', 'ps', 'plyr', 'testthat', 'farver', 'labeling', 'munsell', 'RColorBrewer', 'viridisLite', 'askpass', 'rematch', 'prettyunits', 'processx', 'backports', 'generics', 'reshape2', 'assertthat', 'fansi', 'DBI', 'lifecycle', 'R6', 'tidyselect', 'blob', 'ellipsis', 'vctrs', 'gtable', 'isoband', 'scales', 'withr', 'Rcpp', 'pkgconfig', 'curl', 'openssl', 'utf8', 'clipr', 'BH', 'cellranger', 'progress', 'callr', 'fs', 'whisker', 'selectr', 'broom', 'cli', 'crayon', 'dbplyr', 'dplyr', 'forcats', 'ggplot2', 'haven', 'hms', 'httr', 'lubridate', 'modelr', 'pillar', 'purrr', 'readr', 'readxl', 'reprex', 'rstudioapi', 'rvest', 'tibble', 'tidyr', 'xml2'

    ## 
    ##   There is a binary version available but the source version is later:
    ##         binary source needs_compilation
    ## openssl  1.4.1  1.4.2              TRUE
    ## 
    ## 
    ## The downloaded binary packages are in
    ##  /var/folders/24/8k48jl6d249_n_qfxwsl6xvm0000gn/T//RtmpAjXAZs/downloaded_packages

    ## installing the source package 'openssl'

    ## 
    ## tidyverse installed

    ## Installing package into '/Users/runner/runners/2.263.0/work/_temp/Library'
    ## (as 'lib' is unspecified)

    ## also installing the dependencies 'e1071', 'classInt', 'units', 'sp', 'sf'

    ## 
    ## The downloaded binary packages are in
    ##  /var/folders/24/8k48jl6d249_n_qfxwsl6xvm0000gn/T//RtmpAjXAZs/downloaded_packages

    ## 
    ## rnaturalearth installed

    ## Installing package into '/Users/runner/runners/2.263.0/work/_temp/Library'
    ## (as 'lib' is unspecified)

    ## 
    ## The downloaded binary packages are in
    ##  /var/folders/24/8k48jl6d249_n_qfxwsl6xvm0000gn/T//RtmpAjXAZs/downloaded_packages

    ## 
    ## rnaturalearthdata installed
    ## Installing package into '/Users/runner/runners/2.263.0/work/_temp/Library'
    ## (as 'lib' is unspecified)

    ## 
    ##   There is a binary version available but the source version is later:
    ##        binary source needs_compilation
    ## raster  3.1-5  3.3-7              TRUE

    ## installing the source package 'raster'

    ## 
    ## raster installed

    ## Installing package into '/Users/runner/runners/2.263.0/work/_temp/Library'
    ## (as 'lib' is unspecified)

    ## 
    ## The downloaded binary packages are in
    ##  /var/folders/24/8k48jl6d249_n_qfxwsl6xvm0000gn/T//RtmpAjXAZs/downloaded_packages

    ## 
    ## rgeos installed
    ## Installing package into '/Users/runner/runners/2.263.0/work/_temp/Library'
    ## (as 'lib' is unspecified)

    ## 
    ## The downloaded binary packages are in
    ##  /var/folders/24/8k48jl6d249_n_qfxwsl6xvm0000gn/T//RtmpAjXAZs/downloaded_packages

    ## 
    ## lwgeom installed

    if (!require("rnaturalearthhires")) {
      p_install_gh("ropenscilabs/rnaturalearthhires")
    }

    ## Loading required package: rnaturalearthhires

    ## Using bundled GitHub PAT. Please add your own PAT to the env var `GITHUB_PAT`

    ## Downloading GitHub repo ropenscilabs/rnaturalearthhires@master

    ## 
    ##      checking for file ‘/private/var/folders/24/8k48jl6d249_n_qfxwsl6xvm0000gn/T/RtmpAjXAZs/remotes64542b9662e/ropensci-rnaturalearthhires-2ed7a93/DESCRIPTION’ ...  [32m✔[39m  [90mchecking for file ‘/private/var/folders/24/8k48jl6d249_n_qfxwsl6xvm0000gn/T/RtmpAjXAZs/remotes64542b9662e/ropensci-rnaturalearthhires-2ed7a93/DESCRIPTION’[39m[36m[36m (416ms)[36m[39m
    ##   [90m─[39m[90m  [39m[90mpreparing ‘rnaturalearthhires’:[39m[36m[36m (651ms)[36m[39m
    ##      checking DESCRIPTION meta-information ...  [32m✔[39m  [90mchecking DESCRIPTION meta-information[39m[36m[39m
    ##   [90m─[39m[90m  [39m[90mchecking for LF line-endings in source and make files and shell scripts[39m[36m[39m
    ##   [90m─[39m[90m  [39m[90mchecking for empty or unneeded directories[39m[36m[39m
    ##   [90m─[39m[90m  [39m[90mbuilding ‘rnaturalearthhires_0.2.0.tar.gz’[39m[36m[39m
    ##      
    ## 

    ## Installing package into '/Users/runner/runners/2.263.0/work/_temp/Library'
    ## (as 'lib' is unspecified)

    ## 
    ## The following packages were installed:
    ## rnaturalearthhires

### Add a Shapefile of Australia

This is our base layer, Australia, of the map from
(Naturalearth.com)\[<a href="https://naturalearth.com/" class="uri">https://naturalearth.com/</a>\].

    oz_sf <- rnaturalearth::ne_states(geounit = "australia",
                                      returnclass = "sf")

    plot(oz_sf)

    ## Warning: plotting the first 9 out of 83 attributes; use max.plot = 83 to plot
    ## all

![](README_files/figure-markdown_strict/australia-1.png)

However, it includes several islands and ocean that are not of interest
to us. To fix this, crop it down to just the mainland plus Tasmania and
remove Jervis Bay so that the labels on the final map product are
cleaner.

    oz_sf <- st_intersection(oz_sf,
                             st_set_crs(st_as_sf(as(
                               raster::extent(114,
                                              155,
                                              -45,
                                              -9),
                               "SpatialPolygons"
                             )),
                             st_crs(oz_sf)))

    ## although coordinates are longitude/latitude, st_intersection assumes that they are planar

    ## Warning: attribute variables are assumed to be spatially constant throughout all
    ## geometries

    oz_sf <- oz_sf[oz_sf$abbrev != "J.B.T.",]

    plot(oz_sf)

    ## Warning: plotting the first 9 out of 83 attributes; use max.plot = 83 to plot
    ## all

![](README_files/figure-markdown_strict/crop_sf-1.png)

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

![](README_files/figure-markdown_strict/plot-1.png)

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
    ## Running under: macOS Catalina 10.15.5
    ## 
    ## Matrix products: default
    ## BLAS:   /Library/Frameworks/R.framework/Versions/4.0/Resources/lib/libRblas.dylib
    ## LAPACK: /Library/Frameworks/R.framework/Versions/4.0/Resources/lib/libRlapack.dylib
    ## 
    ## locale:
    ## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ##  [1] lwgeom_0.2-5            rgeos_0.5-3             sf_0.9-4               
    ##  [4] raster_3.3-7            sp_1.4-2                rnaturalearthdata_0.1.0
    ##  [7] rnaturalearth_0.1.0     forcats_0.5.0           stringr_1.4.0          
    ## [10] dplyr_1.0.0             purrr_0.3.4             readr_1.3.1            
    ## [13] tidyr_1.1.0             tibble_3.0.1            ggplot2_3.3.2          
    ## [16] tidyverse_1.3.0         pacman_0.5.1           
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] httr_1.4.1               jsonlite_1.7.0           modelr_0.1.8            
    ##  [4] rnaturalearthhires_0.2.0 assertthat_0.2.1         blob_1.2.1              
    ##  [7] cellranger_1.1.0         yaml_2.2.1               remotes_2.1.1           
    ## [10] pillar_1.4.4             backports_1.1.8          lattice_0.20-41         
    ## [13] glue_1.4.1               digest_0.6.25            RColorBrewer_1.1-2      
    ## [16] rvest_0.3.5              colorspace_1.4-1         htmltools_0.5.0         
    ## [19] pkgconfig_2.0.3          broom_0.5.6              haven_2.3.1             
    ## [22] scales_1.1.1             processx_3.4.2           farver_2.0.3            
    ## [25] generics_0.0.2           ellipsis_0.3.1           withr_2.2.0             
    ## [28] cli_2.0.2                magrittr_1.5             crayon_1.3.4            
    ## [31] readxl_1.3.1             evaluate_0.14            ps_1.3.3                
    ## [34] fs_1.4.1                 fansi_0.4.1              nlme_3.1-148            
    ## [37] xml2_1.3.2               class_7.3-17             pkgbuild_1.0.8          
    ## [40] tools_4.0.2              prettyunits_1.1.1        hms_0.5.3               
    ## [43] lifecycle_0.2.0          munsell_0.5.0            reprex_0.3.0            
    ## [46] callr_3.4.3              compiler_4.0.2           e1071_1.7-3             
    ## [49] rlang_0.4.6              classInt_0.4-3           units_0.6-7             
    ## [52] grid_4.0.2               rstudioapi_0.11          rmarkdown_2.3           
    ## [55] gtable_0.3.0             codetools_0.2-16         DBI_1.1.0               
    ## [58] curl_4.3                 R6_2.4.1                 lubridate_1.7.9         
    ## [61] knitr_1.29               rprojroot_1.3-2          KernSmooth_2.23-17      
    ## [64] stringi_1.4.6            Rcpp_1.0.4.6             vctrs_0.3.1             
    ## [67] dbplyr_1.4.4             tidyselect_1.1.0         xfun_0.15
