# Parallelize spatial computation over multiple raster files

Large raster files usually exceed the memory capacity in size. This
function can be helpful to process heterogenous raster files with
homogeneous summary functions. Heterogenous raster files refer to
rasters with different spatial extents and resolutions. Cropping a large
raster into a small subset even consumes a lot of memory and adds
processing time. This function leverages `terra` `SpatRaster` to
distribute computation jobs over multiple threads. It is assumed that
users have multiple large raster files in their disk, then each file
path is assigned to a thread. Each thread will directly read raster
values from the disk using C++ pointers that operate in terra functions.
For use, it is strongly recommended to use vector data with small and
confined spatial extent for computation to avoid out-of-memory error.
`y` argument in `fun_dist` will be used as-is. That means no
preprocessing or subsetting will be applied. Please be aware of the
spatial extent and size of the inputs.

## Usage

``` r
par_multirasters(filenames, fun_dist, ..., .debug = FALSE)
```

## Arguments

- filenames:

  character. A vector or list of full file paths of raster files. n is
  the total number of raster files.

- fun_dist:

  terra or chopin functions that accept `SpatRaster` object in an
  argument. In particular, `x` and `y` arguments should be present and
  `x` should be a `SpatRaster`.

- ...:

  Arguments passed to the argument `fun_dist`.

- .debug:

  logical(1). Default is `FALSE`. If `TRUE` and a unit computation
  fails, the error message and the file path where the error occurred
  will be included in the output.

## Value

a data.frame object with computation results. For entries of the
results, consult the function used in `fun_dist` argument.

## See also

[`future::multisession`](https://future.futureverse.org/reference/multisession.html),
[`future::multicore`](https://future.futureverse.org/reference/multicore.html),
[`future::cluster`](https://future.futureverse.org/reference/cluster.html),
[`future.mirai::mirai_multisession`](https://future.mirai.futureverse.org/reference/mirai_multisession.html),
[`future::plan`](https://future.futureverse.org/reference/plan.html),
[`par_convert_f`](https://docs.ropensci.org/chopin/reference/par_convert_f.md)

Other Parallelization:
[`par_cut_coords()`](https://docs.ropensci.org/chopin/reference/par_cut_coords.md),
[`par_grid()`](https://docs.ropensci.org/chopin/reference/par_grid.md),
[`par_grid_mirai()`](https://docs.ropensci.org/chopin/reference/par_grid_mirai.md),
[`par_hierarchy()`](https://docs.ropensci.org/chopin/reference/par_hierarchy.md),
[`par_hierarchy_mirai()`](https://docs.ropensci.org/chopin/reference/par_hierarchy_mirai.md),
[`par_make_dggrid()`](https://docs.ropensci.org/chopin/reference/par_make_dggrid.md),
[`par_make_grid()`](https://docs.ropensci.org/chopin/reference/par_make_grid.md),
[`par_make_h3()`](https://docs.ropensci.org/chopin/reference/par_make_h3.md),
[`par_merge_grid()`](https://docs.ropensci.org/chopin/reference/par_merge_grid.md),
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
library(terra)
library(sf)
library(future)
library(future.mirai)
options(sf_use_s2 = FALSE)
future::plan(future.mirai::mirai_multisession, workers = 2)

nccnty <- sf::st_read(
  system.file("shape/nc.shp", package = "sf")
)
#> Reading layer `nc' from data source 
#>   `/github/home/R/x86_64-pc-linux-gnu-library/4.6/sf/shape/nc.shp' 
#>   using driver `ESRI Shapefile'
#> Simple feature collection with 100 features and 14 fields
#> Geometry type: MULTIPOLYGON
#> Dimension:     XY
#> Bounding box:  xmin: -84.32385 ymin: 33.88199 xmax: -75.45698 ymax: 36.58965
#> Geodetic CRS:  NAD27
nccnty <- sf::st_transform(nccnty, "EPSG:5070")

nccntygrid <- sf::st_make_grid(nccnty, n = c(200, 100))
nccntygrid <- sf::st_as_sf(nccntygrid)
nccntygrid$GEOID <- sprintf("%05d", seq_len(nrow(nccntygrid)))
nccntygrid <- sf::st_intersection(nccntygrid, nccnty)
#> Warning: attribute variables are assumed to be spatially constant throughout all geometries

rrast <- terra::rast(nccnty, nrow = 600, ncol = 1320)
terra::values(rrast) <- rgamma(7.92e5, 4, 2)

tdir <- tempdir(check = TRUE)
testpath1 <- file.path(tdir, "test1.tif")
testpath2 <- file.path(tdir, "test2.tif")
terra::writeRaster(rrast, testpath1, overwrite = TRUE)
terra::writeRaster(rrast, testpath2, overwrite = TRUE)
testfiles <- list.files(tdir, pattern = "tif$", full.names = TRUE)

res <- par_multirasters(
  filenames = testfiles,
  fun_dist = extract_at,
  x = testpath1,
  y = nccnty,
  id = "GEOID",
  func = "mean"
)
#> ℹ Input is not a character.
#> Input is a character. Attempt to read it with terra::rast...
#> ℹ Your input function at /tmp/RtmprhXywh/ncelev.tif is dispatched.
#> Input is a character. Attempt to read it with terra::rast...
#> ℹ Your input function at /tmp/RtmprhXywh/test.tif is dispatched.
#> Input is a character. Attempt to read it with terra::rast...
#> ℹ Your input function at /tmp/RtmprhXywh/test1.tif is dispatched.
#> Input is a character. Attempt to read it with terra::rast...
#> ℹ Your input function at /tmp/RtmprhXywh/test2.tif is dispatched.

future::plan(future::sequential)
mirai::daemons(0)
par(lastpar)
# }
```
