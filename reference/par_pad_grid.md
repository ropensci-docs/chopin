# Get a set of computational grids

Using input points, the bounding box is split to the predefined numbers
of columns and rows. Each grid will be buffered by the radius.

## Usage

``` r
par_pad_grid(
  input,
  mode = c("grid", "grid_advanced", "grid_quantile", "h3", "dggrid"),
  nx = 10L,
  ny = 10L,
  grid_min_features = 30L,
  padding = NULL,
  unit = NULL,
  quantiles = NULL,
  merge_max = NULL,
  return_wkt = FALSE,
  res = 8L,
  ...
)
```

## Arguments

- input:

  sf or Spat\* object.

- mode:

  character(1). Mode of region construction. One of

  - `"grid"` (simple grid regardless of the number of features in each
    grid)

  - `"grid_advanced"` (merging adjacent grids with smaller number of
    features than `grid_min_features`). The argument `grid_min_features`
    should be specified.

  - `"grid_quantile"` (x and y quantiles): an argument `quantiles`
    should be specified.

  - `"h3"` (H3 hexagons): an argument `res` should be specified.

  - `"dggrid"` (DGG grids): an argument `res` should be specified.

- nx:

  integer(1). The number of grids along x-axis.

- ny:

  integer(1). The number of grids along y-axis.

- grid_min_features:

  integer(1). A threshold to merging adjacent grids

- padding:

  numeric(1). A extrusion factor to make buffer to clip actual datasets.
  Depending on the length unit of the CRS of input.

- unit:

  character(1). The length unit for padding (optional).
  [`units::set_units`](https://r-quantities.github.io/units/reference/units.html)
  is used for padding when `sf` object is used. See
  [link](https://CRAN.R-project.org/package=units/vignettes/measurement_units_in_R.html)
  for the list of acceptable unit forms.

- quantiles:

  numeric. Quantiles for `grid_quantile` mode.

- merge_max:

  integer(1). Maximum number of grids to merge per merged set.

- return_wkt:

  logical(1). Return WKT format. When `TRUE`, the return value will be a
  list of two WKT strings.

- res:

  integer(1). Resolution for `h3` and `dggrid` modes.

- ...:

  arguments passed to the internal function

## Value

A list of two,

- `original`: exhaustive (filling completely) and non-overlapping grid
  polygons in the class of input

- `padded`: a square buffer of each polygon in `original`. Used for
  computation.

## See also

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
[`par_multirasters()`](https://docs.ropensci.org/chopin/reference/par_multirasters.md),
[`par_multirasters_mirai()`](https://docs.ropensci.org/chopin/reference/par_multirasters_mirai.md),
[`par_pad_balanced()`](https://docs.ropensci.org/chopin/reference/par_pad_balanced.md),
[`par_split_list()`](https://docs.ropensci.org/chopin/reference/par_split_list.md)

## Author

Insang Song

## Examples

``` r
lastpar <- par(mfrow = c(1, 1))
# data
library(sf)
options(sf_use_s2 = FALSE)
ncpath <- system.file("shape/nc.shp", package = "sf")
nc <- read_sf(ncpath)
nc <- st_transform(nc, "EPSG:5070")

# run: nx and ny should strictly be integers
nc_comp_region <-
  par_pad_grid(
    nc,
    mode = "grid",
    nx = 4L, ny = 2L,
    padding = 10000
  )
#> Switch sf class to terra...
#> Switch terra class to sf...
par(mfcol = c(2, 3))
plot(nc_comp_region$original$geometry, main = "Original grid")
plot(nc_comp_region$padded$geometry, main = "Padded grid")

nc_comp_region_wkt <-
  par_pad_grid(
    nc,
    mode = "grid",
    nx = 4L, ny = 2L,
    padding = 10000,
    return_wkt = TRUE
  )
#> Switch sf class to terra...
#> Switch terra class to sf...
nc_comp_region_wkt$original
#> [1] "POLYGON ((1054293 1348021, 1249094 1348021, 1249094 1518623, 1054293 1518623, 1054293 1348021))"
#> [2] "POLYGON ((1249094 1348021, 1443895 1348021, 1443895 1518623, 1249094 1518623, 1249094 1348021))"
#> [3] "POLYGON ((1443895 1348021, 1638695 1348021, 1638695 1518623, 1443895 1518623, 1443895 1348021))"
#> [4] "POLYGON ((1638695 1348021, 1833496 1348021, 1833496 1518623, 1638695 1518623, 1638695 1348021))"
#> [5] "POLYGON ((1054293 1518623, 1249094 1518623, 1249094 1689226, 1054293 1689226, 1054293 1518623))"
#> [6] "POLYGON ((1249094 1518623, 1443895 1518623, 1443895 1689226, 1249094 1689226, 1249094 1518623))"
#> [7] "POLYGON ((1443895 1518623, 1638695 1518623, 1638695 1689226, 1443895 1689226, 1443895 1518623))"
#> [8] "POLYGON ((1638695 1518623, 1833496 1518623, 1833496 1689226, 1638695 1689226, 1638695 1518623))"
nc_comp_region_wkt$padded
#> [1] "POLYGON ((1044293 1338021, 1044293 1528623, 1259094 1528623, 1259094 1338021, 1044293 1338021))"
#> [2] "POLYGON ((1239094 1338021, 1239094 1528623, 1453895 1528623, 1453895 1338021, 1239094 1338021))"
#> [3] "POLYGON ((1433895 1338021, 1433895 1528623, 1648695 1528623, 1648695 1338021, 1433895 1338021))"
#> [4] "POLYGON ((1628695 1338021, 1628695 1528623, 1843496 1528623, 1843496 1338021, 1628695 1338021))"
#> [5] "POLYGON ((1044293 1508623, 1044293 1699226, 1259094 1699226, 1259094 1508623, 1044293 1508623))"
#> [6] "POLYGON ((1239094 1508623, 1239094 1699226, 1453895 1699226, 1453895 1508623, 1239094 1508623))"
#> [7] "POLYGON ((1433895 1508623, 1433895 1699226, 1648695 1699226, 1648695 1508623, 1433895 1508623))"
#> [8] "POLYGON ((1628695 1508623, 1628695 1699226, 1843496 1699226, 1843496 1508623, 1628695 1508623))"

if (rlang::is_installed("h3r")) {
  suppressWarnings(
    nc_comp_region_h3 <-
      par_pad_grid(
        nc,
        mode = "h3",
        res = 4L,
        padding = 10000
      )
  )
  plot(nc_comp_region_h3$original$geometry, main = "H3 grid (lv.4)")
  plot(nc_comp_region_h3$padded$geometry, main = "H3 padded grid (lv.4)")
}
#> Input sf object should be in WGS84 (EPSG:4326) CRS.
#> although coordinates are longitude/latitude, st_intersects assumes that they
#> are planar
#> Switch sf class to terra...
#> Switch terra class to sf...
if (rlang::is_installed("dggridR")) {
  nc_comp_region_dggrid <-
  par_pad_grid(
    nc,
    mode = "dggrid",
    res = 7L,
    padding = 10000
  )
  plot(nc_comp_region_dggrid$original$geometry, main = "DGGRID (lv.7)")
  plot(nc_comp_region_dggrid$padded$geometry, main = "Padded DGGRID (lv.7)")
}
#> Input sf object should be in WGS84 (EPSG:4326) CRS.
#> Switch sf class to terra...
#> Switch terra class to sf...

par(lastpar)
```
