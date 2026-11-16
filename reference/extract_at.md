# Extract raster values with point buffers or polygons

Extract raster values with point buffers or polygons

## Usage

``` r
extract_at(x, y, ...)

# S4 method for class 'SpatRaster,sf'
extract_at(
  x = NULL,
  y = NULL,
  id = NULL,
  func = "mean",
  extent = NULL,
  radius = NULL,
  out_class = "sf",
  kernel = NULL,
  kernel_func = stats::weighted.mean,
  bandwidth = NULL,
  max_cells = 3e+07,
  .standalone = TRUE,
  ...
)

# S4 method for class 'character,character'
extract_at(
  x = NULL,
  y = NULL,
  id = NULL,
  func = "mean",
  extent = NULL,
  radius = NULL,
  out_class = "sf",
  kernel = NULL,
  kernel_func = stats::weighted.mean,
  bandwidth = NULL,
  max_cells = 3e+07,
  .standalone = TRUE,
  ...
)

# S4 method for class 'SpatRaster,character'
extract_at(
  x = NULL,
  y = NULL,
  id = NULL,
  func = "mean",
  extent = NULL,
  radius = NULL,
  out_class = "sf",
  kernel = NULL,
  kernel_func = stats::weighted.mean,
  bandwidth = NULL,
  max_cells = 3e+07,
  .standalone = TRUE,
  ...
)

# S4 method for class 'SpatRaster,SpatVector'
extract_at(
  x = NULL,
  y = NULL,
  id = NULL,
  func = "mean",
  extent = NULL,
  radius = NULL,
  out_class = "sf",
  kernel = NULL,
  kernel_func = stats::weighted.mean,
  bandwidth = NULL,
  max_cells = 3e+07,
  .standalone = TRUE,
  ...
)

# S4 method for class 'character,sf'
extract_at(
  x = NULL,
  y = NULL,
  id = NULL,
  func = "mean",
  extent = NULL,
  radius = NULL,
  out_class = "sf",
  kernel = NULL,
  kernel_func = stats::weighted.mean,
  bandwidth = NULL,
  max_cells = 3e+07,
  .standalone = TRUE,
  ...
)

# S4 method for class 'character,SpatVector'
extract_at(
  x = NULL,
  y = NULL,
  id = NULL,
  func = "mean",
  extent = NULL,
  radius = NULL,
  out_class = "sf",
  kernel = NULL,
  kernel_func = stats::weighted.mean,
  bandwidth = NULL,
  max_cells = 3e+07,
  .standalone = TRUE,
  ...
)
```

## Arguments

- x:

  `SpatRaster` object or file path(s) with extensions that are
  GDAL-compatible. When multiple file paths are used, the rasters must
  have the same extent and resolution.

- y:

  `sf`/`SpatVector` object or file path.

- ...:

  Placeholder.

- id:

  character(1). Unique identifier of each point.

- func:

  function taking one numeric vector argument. Default is `"mean"` for
  all supported signatures in arguments `x` and `y`.

- extent:

  numeric(4) or SpatExtent. Extent of clipping vector. It only works
  with `points` of character(1) file path.

- radius:

  numeric(1). Buffer radius.

- out_class:

  character(1). Output class. One of `sf` or `terra`.

- kernel:

  character(1). Name of a kernel function One of `"uniform"`,
  `"triweight"`, `"quartic"`, and `"epanechnikov"`

- kernel_func:

  function. Kernel function to apply to the extracted values. Default is
  [`stats::weighted.mean()`](https://rdrr.io/r/stats/weighted.mean.html)

- bandwidth:

  numeric(1). Kernel bandwidth.

- max_cells:

  integer(1). Maximum number of cells in memory.

- .standalone:

  logical(1). Default is `TRUE`, which means that the function will be
  executed in a standalone mode. When using this function in `par_*`
  functions, set this to `FALSE`.

## Value

A data.frame object with summarized raster values with respect to the
mode (polygon or buffer) and the function.

## Details

Inputs are preprocessed in different ways depending on the class.

- Vector inputs in `y`: `sf` is preferred, thus character and
  `SpatVector` inputs will be converted to `sf` object. If `radius` is
  not NULL,
  [`sf::st_buffer`](https://r-spatial.github.io/sf/reference/geos_unary.html)
  is used to generate circular buffers as subsequent raster-vector
  overlay is done with
  [`exactextractr::exact_extract`](https://isciences.gitlab.io/exactextractr/reference/exact_extract.html).

- Raster input in `x`: `SpatRaster` is preferred. If the input is not
  `SpatRaster`, it will be converted to `SpatRaster` object.

## See also

Other Macros for calculation:
[`kernelfunction()`](https://docs.ropensci.org/chopin/reference/kernelfunction.md),
[`summarize_aw()`](https://docs.ropensci.org/chopin/reference/summarize_aw.md),
[`summarize_pp()`](https://docs.ropensci.org/chopin/reference/summarize_pp.md),
[`summarize_sedc()`](https://docs.ropensci.org/chopin/reference/summarize_sedc.md),
[`summarize_st()`](https://docs.ropensci.org/chopin/reference/summarize_st.md)

## Author

Insang Song <geoissong@gmail.com>

## Examples

``` r
ncpath <- system.file("gpkg/nc.gpkg", package = "sf")
rastpath <- file.path(tempdir(), "test.tif")

nc <- terra::vect(ncpath)
nc <- terra::project(nc, "EPSG:5070")
rrast <- terra::rast(nc, nrow = 300, ncol = 660)
terra::values(rrast) <- rgamma(1.98e5, 4, 2)
rpnt <- terra::spatSample(rrast, 16L, as.points = TRUE)
rpnt$pid <- sprintf("ID-%02d", seq(1, 16))

extract_at(rrast, rpnt, "pid", "mean", radius = 1000)
#> Switch terra class to sf...
#>      pid     mean
#> 1  ID-01 2.326214
#> 2  ID-02 1.538796
#> 3  ID-03 1.550043
#> 4  ID-04 2.168584
#> 5  ID-05 2.023441
#> 6  ID-06 1.989245
#> 7  ID-07 2.639472
#> 8  ID-08 1.843795
#> 9  ID-09 2.253599
#> 10 ID-10 1.357721
#> 11 ID-11 2.128802
#> 12 ID-12 1.173371
#> 13 ID-13 2.206197
#> 14 ID-14 1.696479
#> 15 ID-15 2.303200
#> 16 ID-16 1.979289
extract_at(rrast, nc, "NAME", "mean")
#> Switch terra class to sf...
#>             NAME     mean
#> 1           Ashe 2.015697
#> 2      Alleghany 1.947710
#> 3          Surry 2.049247
#> 4      Currituck 1.964399
#> 5    Northampton 1.993774
#> 6       Hertford 1.991828
#> 7         Camden 2.022696
#> 8          Gates 2.049954
#> 9         Warren 1.989053
#> 10        Stokes 2.030801
#> 11       Caswell 2.032784
#> 12    Rockingham 2.011434
#> 13     Granville 1.991515
#> 14        Person 1.981381
#> 15         Vance 2.027002
#> 16       Halifax 1.980515
#> 17    Pasquotank 2.018530
#> 18        Wilkes 2.006239
#> 19       Watauga 1.973773
#> 20    Perquimans 2.016622
#> 21        Chowan 1.989094
#> 22         Avery 2.003695
#> 23        Yadkin 1.999365
#> 24      Franklin 1.999179
#> 25       Forsyth 1.980006
#> 26      Guilford 1.988636
#> 27      Alamance 2.036879
#> 28        Bertie 2.020166
#> 29        Orange 2.001364
#> 30        Durham 1.950486
#> 31          Nash 1.925576
#> 32      Mitchell 2.007840
#> 33     Edgecombe 2.026889
#> 34      Caldwell 2.040577
#> 35        Yancey 2.027773
#> 36        Martin 1.973604
#> 37          Wake 2.033633
#> 38       Madison 2.008313
#> 39       Iredell 2.033274
#> 40         Davie 1.886624
#> 41     Alexander 1.935324
#> 42      Davidson 1.999988
#> 43         Burke 1.955401
#> 44    Washington 2.036581
#> 45       Tyrrell 1.962607
#> 46      McDowell 2.010676
#> 47      Randolph 1.989781
#> 48       Chatham 1.997722
#> 49        Wilson 1.972174
#> 50         Rowan 2.013736
#> 51          Pitt 2.026546
#> 52       Catawba 2.013921
#> 53      Buncombe 2.036094
#> 54      Johnston 2.034113
#> 55       Haywood 2.023201
#> 56          Dare 2.022181
#> 57      Beaufort 2.022789
#> 58         Swain 2.059274
#> 59        Greene 1.944938
#> 60           Lee 2.022617
#> 61    Rutherford 1.990824
#> 62         Wayne 1.980167
#> 63       Harnett 2.003174
#> 64     Cleveland 2.039919
#> 65       Lincoln 2.016765
#> 66       Jackson 2.047304
#> 67         Moore 2.012274
#> 68   Mecklenburg 1.967559
#> 69      Cabarrus 2.043931
#> 70    Montgomery 1.979312
#> 71        Stanly 1.949399
#> 72     Henderson 1.991628
#> 73        Graham 1.996089
#> 74        Lenoir 2.017667
#> 75  Transylvania 2.032018
#> 76        Gaston 2.029859
#> 77          Polk 1.998209
#> 78         Macon 1.980021
#> 79       Sampson 1.931533
#> 80       Pamlico 2.023679
#> 81      Cherokee 2.013528
#> 82    Cumberland 1.978953
#> 83         Jones 1.966549
#> 84         Union 2.040053
#> 85         Anson 1.971979
#> 86          Hoke 1.913727
#> 87          Hyde 1.983337
#> 88        Duplin 1.960938
#> 89      Richmond 2.012994
#> 90          Clay 2.045501
#> 91        Craven 1.992210
#> 92      Scotland 1.957454
#> 93        Onslow 2.037033
#> 94       Robeson 2.011626
#> 95      Carteret 2.013820
#> 96        Bladen 1.984599
#> 97        Pender 1.987378
#> 98      Columbus 1.978001
#> 99   New Hanover 1.975845
#> 100    Brunswick 2.008430
extract_at(rrast, ncpath, "NAME", "mean")
#> ℹ Input is a character. Trying to read with sf.
#> Reading layer `nc.gpkg' from data source 
#>   `/github/home/R/x86_64-pc-linux-gnu-library/4.6/sf/gpkg/nc.gpkg' 
#>   using driver `GPKG'
#> Simple feature collection with 100 features and 14 fields
#> Geometry type: MULTIPOLYGON
#> Dimension:     XY
#> Bounding box:  xmin: -84.32385 ymin: 33.88199 xmax: -75.45698 ymax: 36.58965
#> Geodetic CRS:  NAD27
#>             NAME     mean
#> 1           Ashe 2.015697
#> 2      Alleghany 1.947710
#> 3          Surry 2.049247
#> 4      Currituck 1.964399
#> 5    Northampton 1.993774
#> 6       Hertford 1.991828
#> 7         Camden 2.022696
#> 8          Gates 2.049954
#> 9         Warren 1.989053
#> 10        Stokes 2.030801
#> 11       Caswell 2.032784
#> 12    Rockingham 2.011434
#> 13     Granville 1.991515
#> 14        Person 1.981381
#> 15         Vance 2.027002
#> 16       Halifax 1.980515
#> 17    Pasquotank 2.018530
#> 18        Wilkes 2.006239
#> 19       Watauga 1.973773
#> 20    Perquimans 2.016622
#> 21        Chowan 1.989094
#> 22         Avery 2.003695
#> 23        Yadkin 1.999365
#> 24      Franklin 1.999179
#> 25       Forsyth 1.980006
#> 26      Guilford 1.988636
#> 27      Alamance 2.036879
#> 28        Bertie 2.020166
#> 29        Orange 2.001364
#> 30        Durham 1.950486
#> 31          Nash 1.925576
#> 32      Mitchell 2.007840
#> 33     Edgecombe 2.026889
#> 34      Caldwell 2.040577
#> 35        Yancey 2.027773
#> 36        Martin 1.973604
#> 37          Wake 2.033633
#> 38       Madison 2.008313
#> 39       Iredell 2.033274
#> 40         Davie 1.886624
#> 41     Alexander 1.935324
#> 42      Davidson 1.999988
#> 43         Burke 1.955401
#> 44    Washington 2.036581
#> 45       Tyrrell 1.962607
#> 46      McDowell 2.010676
#> 47      Randolph 1.989781
#> 48       Chatham 1.997722
#> 49        Wilson 1.972174
#> 50         Rowan 2.013736
#> 51          Pitt 2.026546
#> 52       Catawba 2.013921
#> 53      Buncombe 2.036094
#> 54      Johnston 2.034113
#> 55       Haywood 2.023201
#> 56          Dare 2.022181
#> 57      Beaufort 2.022789
#> 58         Swain 2.059274
#> 59        Greene 1.944938
#> 60           Lee 2.022617
#> 61    Rutherford 1.990824
#> 62         Wayne 1.980167
#> 63       Harnett 2.003174
#> 64     Cleveland 2.039919
#> 65       Lincoln 2.016765
#> 66       Jackson 2.047304
#> 67         Moore 2.012274
#> 68   Mecklenburg 1.967559
#> 69      Cabarrus 2.043931
#> 70    Montgomery 1.979312
#> 71        Stanly 1.949399
#> 72     Henderson 1.991628
#> 73        Graham 1.996089
#> 74        Lenoir 2.017667
#> 75  Transylvania 2.032018
#> 76        Gaston 2.029859
#> 77          Polk 1.998209
#> 78         Macon 1.980021
#> 79       Sampson 1.931533
#> 80       Pamlico 2.023679
#> 81      Cherokee 2.013528
#> 82    Cumberland 1.978953
#> 83         Jones 1.966549
#> 84         Union 2.040053
#> 85         Anson 1.971979
#> 86          Hoke 1.913727
#> 87          Hyde 1.983337
#> 88        Duplin 1.960938
#> 89      Richmond 2.012994
#> 90          Clay 2.045501
#> 91        Craven 1.992210
#> 92      Scotland 1.957454
#> 93        Onslow 2.037033
#> 94       Robeson 2.011626
#> 95      Carteret 2.013820
#> 96        Bladen 1.984599
#> 97        Pender 1.987378
#> 98      Columbus 1.978001
#> 99   New Hanover 1.975845
#> 100    Brunswick 2.008430
# Using SpatRaster object
suppressWarnings(
  extract_at(
    rrast, ncpath, "NAME", "mean",
    kernel = "epanechnikov",
    bandwidth = 1e5
  )
)
#> ℹ Input is a character. Trying to read with sf.
#> Reading layer `nc.gpkg' from data source 
#>   `/github/home/R/x86_64-pc-linux-gnu-library/4.6/sf/gpkg/nc.gpkg' 
#>   using driver `GPKG'
#> Simple feature collection with 100 features and 14 fields
#> Geometry type: MULTIPOLYGON
#> Dimension:     XY
#> Bounding box:  xmin: -84.32385 ymin: 33.88199 xmax: -75.45698 ymax: 36.58965
#> Geodetic CRS:  NAD27
#> Kernel function [ epanechnikov ] is applied to calculate weights...
#> Switch sf class to terra...
#> # A tibble: 100 × 2
#>    NAME      value
#>    <chr>     <dbl>
#>  1 Alamance   2.04
#>  2 Alexander  1.93
#>  3 Alleghany  1.95
#>  4 Anson      1.97
#>  5 Ashe       2.02
#>  6 Avery      2.00
#>  7 Beaufort   2.02
#>  8 Bertie     2.02
#>  9 Bladen     1.98
#> 10 Brunswick  2.01
#> # ℹ 90 more rows
# Using raster path
terra::writeRaster(rrast, rastpath, overwrite = TRUE)
suppressWarnings(
  extract_at(
    rastpath, ncpath, "NAME", "mean",
    kernel = "epanechnikov",
    bandwidth = 1e5
  )
)
#> Input is a character. Attempt to read it with terra::rast...
#> ℹ Input is a character. Trying to read with sf.
#> Reading layer `nc.gpkg' from data source 
#>   `/github/home/R/x86_64-pc-linux-gnu-library/4.6/sf/gpkg/nc.gpkg' 
#>   using driver `GPKG'
#> Simple feature collection with 100 features and 14 fields
#> Geometry type: MULTIPOLYGON
#> Dimension:     XY
#> Bounding box:  xmin: -84.32385 ymin: 33.88199 xmax: -75.45698 ymax: 36.58965
#> Geodetic CRS:  NAD27
#> Kernel function [ epanechnikov ] is applied to calculate weights...
#> Switch sf class to terra...
#> # A tibble: 100 × 2
#>    NAME      value
#>    <chr>     <dbl>
#>  1 Alamance   2.04
#>  2 Alexander  1.93
#>  3 Alleghany  1.95
#>  4 Anson      1.97
#>  5 Ashe       2.02
#>  6 Avery      2.00
#>  7 Beaufort   2.02
#>  8 Bertie     2.02
#>  9 Bladen     1.98
#> 10 Brunswick  2.01
#> # ℹ 90 more rows
```
