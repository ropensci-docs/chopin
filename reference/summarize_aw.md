# Area weighted summary using two polygon objects

When `x` and `y` are different classes, `poly_weight` will be converted
to the class of `x`.

## Usage

``` r
summarize_aw(x, y, ...)

# S4 method for class 'SpatVector,SpatVector'
summarize_aw(
  x,
  y,
  target_fields = NULL,
  id_x = "ID",
  fun = stats::weighted.mean,
  extent = NULL,
  ...
)

# S4 method for class 'character,character'
summarize_aw(
  x,
  y,
  target_fields = NULL,
  id_x = "ID",
  fun = stats::weighted.mean,
  out_class = "terra",
  extent = NULL,
  ...
)

# S4 method for class 'sf,sf'
summarize_aw(
  x,
  y,
  target_fields = NULL,
  id_x = "ID",
  fun = NULL,
  extent = NULL,
  ...
)
```

## Arguments

- x:

  A sf/SpatVector object or file path of polygons detectable with GDAL
  driver at weighted means will be calculated.

- y:

  A sf/SpatVector object or file path of polygons from which weighted
  means will be calculated.

- ...:

  Additional arguments depending on class of `x` and `y`.

- target_fields:

  character. Field names to calculate area-weighted.

- id_x:

  character(1). The unique identifier of each polygon in `x`. Default is
  `"ID"`.

- fun:

  function(1)/character(1). The function to calculate the weighted
  summary. Default is
  [`stats::weighted.mean`](https://rdrr.io/r/stats/weighted.mean.html).
  The function must have a `w` argument. If both `x` and `y` are `sf`,
  it should be one of `c("sum", "mean")`. It will determine `extensive`
  argument in
  [`sf::st_interpolate_aw`](https://r-spatial.github.io/sf/reference/interpolate_aw.html).

- extent:

  numeric(4) or SpatExtent object. Extent of clipping `x`. It only works
  with `x` of character(1) file path. See
  [`terra::ext`](https://rspatial.github.io/terra/reference/ext.html)
  for more details. Coordinate systems should match.

- out_class:

  character(1). "sf" or "terra". Output class.

## Value

A data.frame with all numeric fields of area-weighted means.

## Note

`x` and `y` classes should match. If `x` and `y` are characters, they
will be read as `sf` objects.

## See also

Other Macros for calculation:
[`extract_at()`](https://docs.ropensci.org/chopin/reference/extract_at.md),
[`kernelfunction()`](https://docs.ropensci.org/chopin/reference/kernelfunction.md),
[`summarize_pp()`](https://docs.ropensci.org/chopin/reference/summarize_pp.md),
[`summarize_sedc()`](https://docs.ropensci.org/chopin/reference/summarize_sedc.md),
[`summarize_st()`](https://docs.ropensci.org/chopin/reference/summarize_st.md)

## Author

Insang Song <geoissong@gmail.com>

## Examples

``` r
lastpar <- par(mfrow = c(1, 1))
# package
library(sf)
options(sf_use_s2 = FALSE)
nc <- sf::st_read(system.file("shape/nc.shp", package="sf"))
#> Reading layer `nc' from data source 
#>   `/github/home/R/x86_64-pc-linux-gnu-library/4.6/sf/shape/nc.shp' 
#>   using driver `ESRI Shapefile'
#> Simple feature collection with 100 features and 14 fields
#> Geometry type: MULTIPOLYGON
#> Dimension:     XY
#> Bounding box:  xmin: -84.32385 ymin: 33.88199 xmax: -75.45698 ymax: 36.58965
#> Geodetic CRS:  NAD27
nc <- sf::st_transform(nc, "EPSG:5070")
pp <- sf::st_sample(nc, size = 300)
pp <- sf::st_as_sf(pp)
pp[["id"]] <- seq(1, nrow(pp))
sf::st_crs(pp) <- "EPSG:5070"
ppb <- sf::st_buffer(pp, nQuadSegs=180, dist = units::set_units(20, "km"))

suppressWarnings(
  ppb_nc_aw <-
    summarize_aw(
      ppb, nc, c("BIR74", "BIR79"),
      "id", fun = "sum"
    )
)
summary(ppb_nc_aw)
#>        id             BIR74              BIR79                     x      
#>  Min.   :  1.00   Min.   :   15.55   Min.   :   31.6   POLYGON      :300  
#>  1st Qu.: 75.75   1st Qu.: 1438.00   1st Qu.: 1726.2   epsg:5070    :  0  
#>  Median :150.50   Median : 2515.48   Median : 3101.5   +proj=aea ...:  0  
#>  Mean   :150.50   Mean   : 2977.28   Mean   : 3782.4                      
#>  3rd Qu.:225.25   3rd Qu.: 3842.13   3rd Qu.: 4779.6                      
#>  Max.   :300.00   Max.   :16849.72   Max.   :23664.8                      

# terra examples
library(terra)
ncpath <- system.file("gpkg/nc.gpkg", package = "sf")
nc <- terra::vect(ncpath)
pp <- terra::spatSample(nc, size = 300)
pp[["id"]] <- seq(1, nrow(pp))
ppb <- terra::buffer(pp, 20000)

suppressWarnings(
  ppb_nc_aw <-
    summarize_aw(
      ppb, nc, c("BIR74", "BIR79"), "id",
      fun = sum
    )
)
summary(ppb_nc_aw)
#>        id             BIR74               BIR79          
#>  Min.   :  1.00   Min.   :3.754e+08   Min.   :3.754e+08  
#>  1st Qu.: 75.75   1st Qu.:1.153e+09   1st Qu.:1.153e+09  
#>  Median :150.50   Median :1.251e+09   Median :1.251e+09  
#>  Mean   :150.50   Mean   :1.151e+09   Mean   :1.151e+09  
#>  3rd Qu.:225.25   3rd Qu.:1.251e+09   3rd Qu.:1.252e+09  
#>  Max.   :300.00   Max.   :1.252e+09   Max.   :1.252e+09  
par(lastpar)
```
