# Convert DGGRID indices to sf object

This function converts DGGRID indices to an `sf` object. It requires the
`dggridR` package to be installed.

## Usage

``` r
par_make_dggrid(x, res = 8L, topology = "HEXAGON")
```

## Arguments

- x:

  sf object.

- res:

  integer(1). DGGRID resolution. Default is 8L.

- topology:

  character(1). Topology type, either "HEXAGON" or "SQUARE". Default is
  "HEXAGON".

## Value

An `sf` object with polygons representing the DGGRID indices.

## Details

[`dggridR::dgconstruct`](https://rdrr.io/pkg/dggridR/man/dgconstruct.html)
is used to create a DGGRID object with the specified resolution. All
arguments in this function are used as default values other than `res`
and `topology`.

## See also

Other Parallelization:
[`par_cut_coords()`](https://docs.ropensci.org/chopin/reference/par_cut_coords.md),
[`par_grid()`](https://docs.ropensci.org/chopin/reference/par_grid.md),
[`par_grid_mirai()`](https://docs.ropensci.org/chopin/reference/par_grid_mirai.md),
[`par_hierarchy()`](https://docs.ropensci.org/chopin/reference/par_hierarchy.md),
[`par_hierarchy_mirai()`](https://docs.ropensci.org/chopin/reference/par_hierarchy_mirai.md),
[`par_make_grid()`](https://docs.ropensci.org/chopin/reference/par_make_grid.md),
[`par_make_h3()`](https://docs.ropensci.org/chopin/reference/par_make_h3.md),
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
if (rlang::is_installed("dggridR")) {
  library(dggridR)
  options(sf_use_s2 = FALSE)
  ncpath <- system.file("shape/nc.shp", package = "sf")
  nc <- read_sf(ncpath)
  nc <- st_transform(nc, "EPSG:4326")
  nc_comp_region_dggrid <-
    par_make_dggrid(
      nc,
      res = 8L,
      topology = "HEXAGON"
    )
  plot(sf::st_geometry(nc_comp_region_dggrid))
}
#> Input sf object should be in WGS84 (EPSG:4326) CRS.

par(lastpar)
```
