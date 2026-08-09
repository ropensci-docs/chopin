# Parallelize spatial computation by hierarchy in input data

"Hierarchy" refers to a system, which divides the entire study region
into multiple subregions. It is usually reflected in an area code system
(e.g., FIPS for US Census geographies and Nomenclature of Territorial
Units for Statistics (NUTS), etc.).
[mirai::daemons](https://mirai.r-lib.org/reference/daemons.html) will
set the parallel backend then
[mirai::mirai_map](https://mirai.r-lib.org/reference/mirai_map.html)
will the work by splitting lower level features into several higher
level feature group. For details of the terminology in `mirai` package,
refer to [`mirai::mirai`](https://mirai.r-lib.org/reference/mirai.html).
Each thread will process the number of lower level features in each
higher level feature. Be advised that accessing the same file
simultaneously with multiple processes may result in errors.

## Usage

``` r
par_hierarchy_mirai(
  regions,
  regions_id = NULL,
  length_left = NULL,
  pad = 0,
  pad_y = FALSE,
  fun_dist,
  ...,
  .debug = TRUE
)
```

## Arguments

- regions:

  `sf`/`SpatVector` object. Computational regions. Only polygons are
  accepted.

- regions_id:

  character(1). Name of unique ID field in `regions`. The regions will
  be split by the common level value.

- length_left:

  integer(1). Length of the first characters of the `regions_id` values.
  Default is NULL, which will not manipulate the `regions_id` values. If
  the number of characters is not consistent (for example, numerics),
  the function will alert the user.

- pad:

  numeric(1). Padding distance for each subregion defined by
  `regions_id` or trimmed `regions_id` values. in linear unit of
  coordinate system. Default is 0, which means each subregion is used as
  is. If the value is greater than 0, the subregion will be buffered by
  the value. The padding distance will be applied to `x`
  (`pad_y = FALSE`) or `y` (`pad_y = TRUE`) to filter the data.

- pad_y:

  logical(1). Whether to filter y with the padded grid. Should be TRUE
  when x is where the values are calculated. Default is `FALSE`. In the
  reverse case, like `terra::extent` or
  [`exactextractr::exact_extract`](https://isciences.gitlab.io/exactextractr/reference/exact_extract.html),
  the raster (x) should be scoped with the padded grid.

- fun_dist:

  `sf`, `terra`, or `chopin` functions. This function should have `x`
  and `y` arguments.

- ...:

  Arguments passed to the argument `fun_dist`.

- .debug:

  logical(1). Default is `FALSE` If a unit computation fails, the error
  message and the `regions_id` value where the error occurred will be
  included in the output.

## Value

a data.frame object with computation results. For entries of the
results, consult the function used in `fun_dist` argument.

## Details

In dynamic dots (`...`), `fun_dist` arguments should include x and y
where sf/terra class objects or file paths are accepted. Hierarchy is
interpreted by the `regions_id` argument first. `regions_id` is assumed
to be a field name in the `x` or `y` argument object. It is expected
that `regions` represents the higher level boundaries and `x` or `y` in
`fun_dist` is the lower level boundaries. However, if that is not the
case, with `trim` argument, the function will generate the higher level
codes from `regions_id` by extracting the code from the left end
(controlled by `length_left`). Whether `x` or `y` is searched is
determined by `pad_y` value. `pad_y = TRUE` will make the function
attempt to find `regions_id` in `x`, whereas `pad_y = FALSE` will look
for `regions_id` at `y`. If the `regions_id` doesn't exist in `x` or
`y`, the function will utilize spatial relationship (intersects) to
filter the data. Note that dispatching computation by subregions based
on the spatial relationship may lead to a slight discrepancy in the
result. For example, if the higher and lower level features are not
perfectly aligned, there may be some features that are not included or
duplicated in the subregions. The function will alert the user if
spatial relation- ship is used to filter the data.

## Note

Virtually any sf/terra functions that accept two arguments can be put in
`fun_dist`; however, be advised that some spatial operations do not
necessarily give the exact result from what would have been done with
one thread. For example, distance calculated through this function may
return the lower value than actual because the computational region was
reduced. This would be the case especially where the target features are
spatially sparsely distributed.

## See also

[`mirai::mirai_map`](https://mirai.r-lib.org/reference/mirai_map.html),
[`mirai::daemons`](https://mirai.r-lib.org/reference/daemons.html),
[`par_convert_f`](https://docs.ropensci.org/chopin/reference/par_convert_f.md)

Other Parallelization:
[`par_cut_coords()`](https://docs.ropensci.org/chopin/reference/par_cut_coords.md),
[`par_grid()`](https://docs.ropensci.org/chopin/reference/par_grid.md),
[`par_grid_mirai()`](https://docs.ropensci.org/chopin/reference/par_grid_mirai.md),
[`par_hierarchy()`](https://docs.ropensci.org/chopin/reference/par_hierarchy.md),
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
library(terra)
library(sf)
library(mirai)
options(sf_use_s2 = FALSE)
mirai::daemons(4)

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

# Using raster path
rastpath <- file.path(tempdir(), "ncelev.tif")
terra::writeRaster(rrast, rastpath, overwrite = TRUE)

ncsamp <-
  sf::st_sample(
    nccnty,
    size = 1e4L
  )
# sfc to sf
ncsamp <- sf::st_as_sf(ncsamp)
# assign ID
ncsamp$kid <- sprintf("K-%05d", seq_len(nrow(ncsamp)))
res <-
  par_hierarchy_mirai(
    regions = nccnty,
    regions_id = "FIPS",
    fun_dist = extract_at,
    y = nccntygrid,
    x = rastpath,
    id = "GEOID",
    func = "mean",
    .debug = TRUE
  )
#> ℹ Input is not a character.
#> ℹ FIPS is used to stratify the process.
#> ■                                  1% | ETA:  5m
#> ■■■■■■■■■■■■■■■■■■■               60% | ETA:  4s
#> ■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■  100% | ETA:  0s
mirai::daemons(0L)
par(lastpar)
# }
```
