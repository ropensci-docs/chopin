# targets and grid objects

## Objective

`targets` is a powerful workflow management for reproducibility.
`chopin` grid partitioning is a way to parallelize the repeated tasks
across unit grids by applying patterns. This vignette demonstrates how
to use `targets` and `chopin` together.

## Installation

Despite the `targets` is not referenced in the `DESCRIPTION` file, it is
required to install `targets` package to run the code in this vignette.

``` r

rlang::check_installed("targets")
```

## Example

[`par_pad_grid()`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md)
or
[`par_pad_balanced()`](https://docs.ropensci.org/chopin/reference/par_pad_balanced.md)
functions have an argument `return_wkt` to return the grid partition as
well-known text (WKT) format characters. This format is exported to the
parallel workers regardless of the parallel backend such as
[`future::multisession`](https://future.futureverse.org/reference/multisession.html)
and [`mirai::daemons`](https://mirai.r-lib.org/reference/daemons.html),
which cannot interoperate with `externalpnt` objects for C++ functions.
Using WKT character objects, we can easily convert them to `sf` or
`terra` objects **inside** a function running on a parallel worker and
use them in the `targets` workflow with standard branching/patterning
interface such as `map()`, `cross()`, and others.

The example below will generate a grid partition of the North Carolina
state and demonstrate how to use the grid partition in the `targets`
workflow.

### Random points in NC

- For demonstration of
  [`par_pad_grid()`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md),
  we use moderately clustered point locations generated inside the
  counties of North Carolina.

``` r

library(chopin)
library(sf)
library(spatstat.random)

sf::sf_use_s2(FALSE)
set.seed(202404)
```

``` r

ncpoly <- system.file("shape/nc.shp", package = "sf")
ncsf <- sf::read_sf(ncpoly)
ncsf <- sf::st_transform(ncsf, "EPSG:5070")
plot(sf::st_geometry(ncsf))
```

![](v05_targets_files/figure-html/nc-gen-points-1.png)

``` r

ncpoints <-
  sf::st_sample(
    x = ncsf,
    type = "Thomas",
    mu = 20,
    scale = 1e4,
    kappa = 1.25e-9
  )
ncpoints <- sf::st_as_sf(ncpoints)
ncpoints <- sf::st_set_crs(ncpoints, "EPSG:5070")
ncpoints$pid <- sprintf("PID-%05d", seq(1, nrow(ncpoints)))
plot(sf::st_geometry(ncpoints))
```

![](v05_targets_files/figure-html/nc-gen-points-2.png)

### Grid partition of NC

``` r

ncgrid_sf <-
  par_pad_grid(
    input = ncpoints,
    mode = "grid",
    nx = 6L,
    ny = 3L,
    padding = 1e4L,
    return_wkt = FALSE
  )

ncgrid_sf$original
```

    ## Simple feature collection with 18 features and 1 field
    ## Geometry type: POLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 1057207 ymin: 1355820 xmax: 1830518 ymax: 1676480
    ## Projected CRS: NAD83 / Conus Albers
    ## First 10 features:
    ##                          geometry CGRIDID
    ## 1  POLYGON ((1057207 1355820, ...       1
    ## 2  POLYGON ((1186093 1355820, ...       2
    ## 3  POLYGON ((1314978 1355820, ...       3
    ## 4  POLYGON ((1443863 1355820, ...       4
    ## 5  POLYGON ((1572748 1355820, ...       5
    ## 6  POLYGON ((1701633 1355820, ...       6
    ## 7  POLYGON ((1057207 1462707, ...       7
    ## 8  POLYGON ((1186093 1462707, ...       8
    ## 9  POLYGON ((1314978 1462707, ...       9
    ## 10 POLYGON ((1443863 1462707, ...      10

``` r

ncgrid_sf$padded
```

    ## Simple feature collection with 18 features and 1 field
    ## Geometry type: POLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 1047207 ymin: 1345820 xmax: 1840518 ymax: 1686480
    ## Projected CRS: NAD83 / Conus Albers
    ## First 10 features:
    ##    CGRIDID                       geometry
    ## 1        1 POLYGON ((1047207 1345820, ...
    ## 2        2 POLYGON ((1176093 1345820, ...
    ## 3        3 POLYGON ((1304978 1345820, ...
    ## 4        4 POLYGON ((1433863 1345820, ...
    ## 5        5 POLYGON ((1562748 1345820, ...
    ## 6        6 POLYGON ((1691633 1345820, ...
    ## 7        7 POLYGON ((1047207 1452707, ...
    ## 8        8 POLYGON ((1176093 1452707, ...
    ## 9        9 POLYGON ((1304978 1452707, ...
    ## 10      10 POLYGON ((1433863 1452707, ...

Since `sf` objects are exportable to the parallel workers, we can also
consider these as a part of the `targets` workflow.

``` r

ncgrid_wkt <-
  par_pad_grid(
    input = ncpoints,
    mode = "grid",
    nx = 6L,
    ny = 3L,
    padding = 1e4L,
    return_wkt = TRUE
  )

ncgrid_wkt$original
```

    ##  [1] "POLYGON ((1057207 1355820, 1186093 1355820, 1186093 1462707, 1057207 1462707, 1057207 1355820))"
    ##  [2] "POLYGON ((1186093 1355820, 1314978 1355820, 1314978 1462707, 1186093 1462707, 1186093 1355820))"
    ##  [3] "POLYGON ((1314978 1355820, 1443863 1355820, 1443863 1462707, 1314978 1462707, 1314978 1355820))"
    ##  [4] "POLYGON ((1443863 1355820, 1572748 1355820, 1572748 1462707, 1443863 1462707, 1443863 1355820))"
    ##  [5] "POLYGON ((1572748 1355820, 1701633 1355820, 1701633 1462707, 1572748 1462707, 1572748 1355820))"
    ##  [6] "POLYGON ((1701633 1355820, 1830518 1355820, 1830518 1462707, 1701633 1462707, 1701633 1355820))"
    ##  [7] "POLYGON ((1057207 1462707, 1186093 1462707, 1186093 1569593, 1057207 1569593, 1057207 1462707))"
    ##  [8] "POLYGON ((1186093 1462707, 1314978 1462707, 1314978 1569593, 1186093 1569593, 1186093 1462707))"
    ##  [9] "POLYGON ((1314978 1462707, 1443863 1462707, 1443863 1569593, 1314978 1569593, 1314978 1462707))"
    ## [10] "POLYGON ((1443863 1462707, 1572748 1462707, 1572748 1569593, 1443863 1569593, 1443863 1462707))"
    ## [11] "POLYGON ((1572748 1462707, 1701633 1462707, 1701633 1569593, 1572748 1569593, 1572748 1462707))"
    ## [12] "POLYGON ((1701633 1462707, 1830518 1462707, 1830518 1569593, 1701633 1569593, 1701633 1462707))"
    ## [13] "POLYGON ((1057207 1569593, 1186093 1569593, 1186093 1676480, 1057207 1676480, 1057207 1569593))"
    ## [14] "POLYGON ((1186093 1569593, 1314978 1569593, 1314978 1676480, 1186093 1676480, 1186093 1569593))"
    ## [15] "POLYGON ((1314978 1569593, 1443863 1569593, 1443863 1676480, 1314978 1676480, 1314978 1569593))"
    ## [16] "POLYGON ((1443863 1569593, 1572748 1569593, 1572748 1676480, 1443863 1676480, 1443863 1569593))"
    ## [17] "POLYGON ((1572748 1569593, 1701633 1569593, 1701633 1676480, 1572748 1676480, 1572748 1569593))"
    ## [18] "POLYGON ((1701633 1569593, 1830518 1569593, 1830518 1676480, 1701633 1676480, 1701633 1569593))"

``` r

ncgrid_wkt$padded
```

    ##  [1] "POLYGON ((1047207 1345820, 1047207 1472707, 1196093 1472707, 1196093 1345820, 1047207 1345820))"
    ##  [2] "POLYGON ((1176093 1345820, 1176093 1472707, 1324978 1472707, 1324978 1345820, 1176093 1345820))"
    ##  [3] "POLYGON ((1304978 1345820, 1304978 1472707, 1453863 1472707, 1453863 1345820, 1304978 1345820))"
    ##  [4] "POLYGON ((1433863 1345820, 1433863 1472707, 1582748 1472707, 1582748 1345820, 1433863 1345820))"
    ##  [5] "POLYGON ((1562748 1345820, 1562748 1472707, 1711633 1472707, 1711633 1345820, 1562748 1345820))"
    ##  [6] "POLYGON ((1691633 1345820, 1691633 1472707, 1840518 1472707, 1840518 1345820, 1691633 1345820))"
    ##  [7] "POLYGON ((1047207 1452707, 1047207 1579593, 1196093 1579593, 1196093 1452707, 1047207 1452707))"
    ##  [8] "POLYGON ((1176093 1452707, 1176093 1579593, 1324978 1579593, 1324978 1452707, 1176093 1452707))"
    ##  [9] "POLYGON ((1304978 1452707, 1304978 1579593, 1453863 1579593, 1453863 1452707, 1304978 1452707))"
    ## [10] "POLYGON ((1433863 1452707, 1433863 1579593, 1582748 1579593, 1582748 1452707, 1433863 1452707))"
    ## [11] "POLYGON ((1562748 1452707, 1562748 1579593, 1711633 1579593, 1711633 1452707, 1562748 1452707))"
    ## [12] "POLYGON ((1691633 1452707, 1691633 1579593, 1840518 1579593, 1840518 1452707, 1691633 1452707))"
    ## [13] "POLYGON ((1047207 1559593, 1047207 1686480, 1196093 1686480, 1196093 1559593, 1047207 1559593))"
    ## [14] "POLYGON ((1176093 1559593, 1176093 1686480, 1324978 1686480, 1324978 1559593, 1176093 1559593))"
    ## [15] "POLYGON ((1304978 1559593, 1304978 1686480, 1453863 1686480, 1453863 1559593, 1304978 1559593))"
    ## [16] "POLYGON ((1433863 1559593, 1433863 1686480, 1582748 1686480, 1582748 1559593, 1433863 1559593))"
    ## [17] "POLYGON ((1562748 1559593, 1562748 1686480, 1711633 1686480, 1711633 1559593, 1562748 1559593))"
    ## [18] "POLYGON ((1691633 1559593, 1691633 1686480, 1840518 1686480, 1840518 1559593, 1691633 1559593))"

### Targets workflow

Assume that we design a function `calc_something()` that calculates
something from the grid partition. We can use the grid partition as an
input to the function. In `sf` object centered workflow, we can use `sf`
functions to interact with the exported grid partition objects. Let’s
consider a binary spatial operation where `x` and `y` are involved. `x`
is a dataset at the variable is calculated whereas `y` is a raster file
path from which we extract the values. Please note that SpatRaster
objects cannot be exported to parallel workers as it is. We will read
the object in parallel workers. To branch out across the grid partition,
the function for the unit grid should handle subsetting `x` to narrow
down the calculation scope to each grid. Therefore, a synopsis of the
function should look like this:

``` r

calc_something <- function(x, y, unit_grid, pad_grid, ...) {
  # 0. restore unit_grid and pad_grid to sf objects if they are in WKT format
  # 1-1. make x subset using intersect logic between x and unit_grid
  # 1-2. read y subset using intersect logic between y and pad_grid
  # 2. make buffer of x
  # 3. do actual calculation (use ... wisely to pass additional arguments)
  # 4. return the result
}
```

`map(unit_grid, pad_grid)` to `pattern` argument `tar_target()` will do
it for you.

``` r

calc_something <- function(x, y, unit_grid, pad_grid, ...) {
  # 1-1. make x subset using intersect logic between x and unit_grid
  x <- x[unit_grid, ]
  # 1-2. read y subset using intersect logic between y and pad_grid
  yext <- terra::ext(sf::st_bbox(pad_grid))
  yras <- terra::rast(y, win = yext)
  # 2. make buffer of x
  xbuffer <- sf::st_buffer(x, units::set_units(10, "km"))
  # 3. do actual calculation (use ... wisely to pass additional arguments)
  xycalc <- exactextractr::exact_extract(
    yras,
    xbuffer,
    force_df = TRUE,
    fun = "mean",
    append_cols = "pid", # assume that pid is a unique identifier
    progress = FALSE
  )
  # 4. return the result
  return(xycalc)
}
```

`sf` object inherits `data.frame` class. To align this object with
`targets` branching, it will be clear to convert this object into a
`list` object to pattern across the grid partition. `par_split_list` in
chopin does it for you.

``` r

ncgrid_sflist <-
  par_split_list(ncgrid_sf)
```

When WKT format is used, the function should be modified to restore the
grid partition to `sf` objects. The function should be modified as
follows:

``` r

calc_something <- function(x, y, unit_grid, pad_grid, ...) {
  # 0. restore unit_grid and pad_grid to sf objects if they are in WKT format
  unit_grid <- sf::st_as_sf(wkt = unit_grid)
  pad_grid <- sf::st_as_sf(wkt = pad_grid)
  # 1-1. make x subset using intersect logic between x and unit_grid
  x <- x[unit_grid, ]
  # 1-2. read y subset using intersect logic between y and pad_grid
  yext <- terra::ext(sf::st_bbox(pad_grid))
  yras <- terra::rast(y, win = yext)
  # 2. make buffer of x
  xbuffer <- sf::st_buffer(x, units::set_units(10, "km"))
  # 3. do actual calculation (use ... wisely to pass additional arguments)
  xycalc <- exactextractr::exact_extract(
    yras,
    xbuffer,
    fun = "mean",
    force_df = TRUE,
    append_cols = "pid", # assume that pid is a unique identifier
    progress = FALSE
  )
  # 4. return the result
  return(xycalc)
}
```

``` r

ncgrid_wktlist <-
  par_split_list(ncgrid_wkt)
```

`tar_target` can use this list object with our function `calc_something`
to branch out. A workable example of `tar_target` with a proper
\_targets.R file is as follows:

``` r

list(
  tar_target(
    name = points,
    command = sf::st_read("path_to_points.format")
  ),
  tar_target(
    name = raster,
    command = "path_to_raster.format",
    format = "file"
  ),
  tar_target(
    name = chopingrid,
    command = par_pad_grid(points, input = points, nx = 6L, ny = 3L, padding = 1e4L, return_wkt = FALSE)
  ),
  tar_target(
    name = chopingrid_split,
    command = mapply(
      function(listorig, row) {
        list(listorig$original[row, ], listorig$padded[row, ])
      },
      chopingrid, seq_len(nrow(chopingrid$original)),
      SIMPLIFY = FALSE
    ),
    iteration = "list"
  ),
  tar_target(
    name = result,
    command =
    calc_something(
      points, raster,
      chopingrid_split[[1]], chopingrid_split[[2]]
    ),
    pattern = map(chopingrid_split),
    iteration = "list"
  )
)
```

The target `result` will be a list of `data.frame`s that contain the
calculation results.
