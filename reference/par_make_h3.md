# Convert a input sf object to H3 hexagons

This function converts an input `sf` to an `sf` object with H3 hexagons.
It requires the `h3r` package to be installed.

## Usage

``` r
par_make_h3(x, res = 5L)
```

## Arguments

- x:

  sf object.

- res:

  integer(1). H3 resolution. Default is 5L.

## Value

An `sf` object with polygons representing the H3 indices.

## Details

Non-polygon `x` will be converted to polygons using
[`sf::st_concave_hull`](https://r-spatial.github.io/sf/reference/geos_unary.html).
If the input is not convertible to polygons, the function will throw an
error.

## See also

Other Parallelization:
[`par_cut_coords()`](https://docs.ropensci.org/chopin/reference/par_cut_coords.md),
[`par_grid()`](https://docs.ropensci.org/chopin/reference/par_grid.md),
[`par_grid_mirai()`](https://docs.ropensci.org/chopin/reference/par_grid_mirai.md),
[`par_hierarchy()`](https://docs.ropensci.org/chopin/reference/par_hierarchy.md),
[`par_hierarchy_mirai()`](https://docs.ropensci.org/chopin/reference/par_hierarchy_mirai.md),
[`par_make_dggrid()`](https://docs.ropensci.org/chopin/reference/par_make_dggrid.md),
[`par_make_grid()`](https://docs.ropensci.org/chopin/reference/par_make_grid.md),
[`par_merge_grid()`](https://docs.ropensci.org/chopin/reference/par_merge_grid.md),
[`par_multirasters()`](https://docs.ropensci.org/chopin/reference/par_multirasters.md),
[`par_multirasters_mirai()`](https://docs.ropensci.org/chopin/reference/par_multirasters_mirai.md),
[`par_pad_balanced()`](https://docs.ropensci.org/chopin/reference/par_pad_balanced.md),
[`par_pad_grid()`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md),
[`par_split_list()`](https://docs.ropensci.org/chopin/reference/par_split_list.md)

## Author

Insang Song

## Examples

``` r
lastpar <- par(mfrow = c(1, 1))
library(sf)
if (rlang::is_installed("h3r")) {
library(h3r)
options(sf_use_s2 = FALSE)
ncpath <- system.file("shape/nc.shp", package = "sf")
nc <- read_sf(ncpath)
nc <- st_transform(nc, "EPSG:4326")
# note that it will throw a warning if
# the input is MULTIPOLYGON.
nc_comp_region_h3 <-
  suppressWarnings(
    par_make_h3(
      nc,
      res = 5L
    )
  )
plot(sf::st_geometry(nc_comp_region_h3))
}
#> Loading required package: h3lib
#> 
#> Attaching package: ‘h3r’
#> The following object is masked from ‘package:terra’:
#> 
#>     gridDistance
#> Input sf object should be in WGS84 (EPSG:4326) CRS.
#> although coordinates are longitude/latitude, st_intersects assumes that they
#> are planar

par(lastpar)
```
