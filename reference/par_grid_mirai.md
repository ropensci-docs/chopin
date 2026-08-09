# Parallelize spatial computation over the computational grids

[mirai::daemons](https://mirai.r-lib.org/reference/daemons.html) will
set the parallel backend then
[mirai::mirai_map](https://mirai.r-lib.org/reference/mirai_map.html)
will parallelize the work in each grid. For details of the terminology
in `mirai` package, refer to
[`mirai::mirai`](https://mirai.r-lib.org/reference/mirai.html). This
function assumes that users have one raster file and a sizable and
spatially distributed target locations. Each thread will process the
nearest integer of \$\|N_g\| / \|N_t\|\$ grids where \$\|N_g\|\$ denotes
the number of grids and \$\|N_t\|\$ denotes the number of threads.

## Usage

``` r
par_grid_mirai(grids, fun_dist, ..., pad_y = FALSE, .debug = TRUE)
```

## Arguments

- grids:

  List of two sf/SpatVector objects. Computational grids. It takes a
  strict assumption that the grid input is an output of `par_pad_grid`.

- fun_dist:

  `sf`, `terra` or `chopin` functions. This function should have `x` and
  `y` arguments.

- ...:

  Arguments passed to the argument `fun_dist`.

- pad_y:

  logical(1). Whether to filter y with the padded grid. Should be TRUE
  when x is where the values are calculated. Default is `FALSE`. In the
  reverse case, like `terra::extent` or
  [`exactextractr::exact_extract`](https://isciences.gitlab.io/exactextractr/reference/exact_extract.html),
  the raster (x) extent should be set with the padded grid.

- .debug:

  logical(1). Default is `FALSE`. Otherwise, if a unit computation
  fails, the error message and the `CGRIDID` value where the error
  occurred will be included in the output.

## Value

a data.frame object with computation results. For entries of the
results, consult the documentation of the function put in `fun_dist`
argument.

## Note

In dynamic dots (`...`), `fun_dist` arguments should include x and y
where sf/terra class objects or file paths are accepted. Virtually any
sf/terra functions that accept two arguments can be put in `fun_dist`;
however be advised that some spatial operations do not necessarily give
the exact result from what would have been done with one thread. For
example, distance calculated through this function may return the lower
value than actual because the computational region was reduced. This
would be the case especially where the target features are spatially
sparsely distributed.

## See also

[`mirai::daemons`](https://mirai.r-lib.org/reference/daemons.html),
[`mirai::mirai_map`](https://mirai.r-lib.org/reference/mirai_map.html),
[`par_convert_f`](https://docs.ropensci.org/chopin/reference/par_convert_f.md)

Other Parallelization:
[`par_cut_coords()`](https://docs.ropensci.org/chopin/reference/par_cut_coords.md),
[`par_grid()`](https://docs.ropensci.org/chopin/reference/par_grid.md),
[`par_hierarchy()`](https://docs.ropensci.org/chopin/reference/par_hierarchy.md),
[`par_hierarchy_mirai()`](https://docs.ropensci.org/chopin/reference/par_hierarchy_mirai.md),
[`par_make_dggrid()`](https://docs.ropensci.org/chopin/reference/par_make_dggrid.md),
[`par_make_grid()`](https://docs.ropensci.org/chopin/reference/par_make_grid.md),
[`par_make_h3()`](https://docs.ropensci.org/chopin/reference/par_make_h3.md),
[`par_merge_grid()`](https://docs.ropensci.org/chopin/reference/par_merge_grid.md),
[`par_multirasters()`](https://docs.ropensci.org/chopin/reference/par_multirasters.md),
[`par_multirasters_mirai()`](https://docs.ropensci.org/chopin/reference/par_multirasters_mirai.md),
[`par_pad_balanced()`](https://docs.ropensci.org/chopin/reference/par_pad_balanced.md),
[`par_pad_grid()`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md),
[`par_split_list()`](https://docs.ropensci.org/chopin/reference/par_split_list.md)

## Author

Insang Song <geoissong@gmail.com>

## Examples

``` r
# \donttest{
lastpar <- par(mfrow = c(1, 1))
library(sf)
library(mirai)
options(sf_use_s2 = FALSE)
daemons(4)
ncpath <- system.file("shape/nc.shp", package = "sf")
ncpoly <- sf::st_read(ncpath)
#> Reading layer `nc' from data source 
#>   `/github/home/R/x86_64-pc-linux-gnu-library/4.6/sf/shape/nc.shp' 
#>   using driver `ESRI Shapefile'
#> Simple feature collection with 100 features and 14 fields
#> Geometry type: MULTIPOLYGON
#> Dimension:     XY
#> Bounding box:  xmin: -84.32385 ymin: 33.88199 xmax: -75.45698 ymax: 36.58965
#> Geodetic CRS:  NAD27
ncpoly <- sf::st_transform(ncpoly, "EPSG:5070")

# sf object
ncpnts <-
  sf::st_sample(ncpoly, 2000)
ncpnts <- sf::st_as_sf(ncpnts)
ncpnts$pid <- seq_len(nrow(ncpnts))

# file path
rrast <- terra::rast(ncpoly, nrow = 600, ncol = 1320)
terra::values(rrast) <- rgamma(7.92e5, 4, 2)
# Using raster path
rastpath <- file.path(tempdir(), "ncelev.tif")
terra::writeRaster(rrast, rastpath, overwrite = TRUE)

nccompreg <-
  chopin::par_pad_grid(
    input = ncpnts,
    mode = "grid",
    nx = 4L,
    ny = 2L,
    padding = 5e3L
  )
#> Switch sf class to terra...
#> Switch terra class to sf...
res <-
  par_grid_mirai(
    grids = nccompreg,
    fun_dist = extract_at,
    x = rastpath,
    y = ncpnts,
    qsegs = 90L,
    radius = 5e3L,
    id = "pid"
  )
#> ℹ Input is not a character.
#> ■■■■■                             12% | ETA: 42s
#> ■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■  100% | ETA:  0s
mirai::daemons(0L)
par(lastpar)
# }
```
