# Extension of par_make_balanced for padded grids

This function utilizes
[`anticlust::balanced_clustering()`](https://m-py.github.io/anticlust/reference/balanced_clustering.html)
to split the input into equal size subgroups then transform the data to
be compatible with the output of
[`par_pad_grid`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md),
for which a set of padded grids of the extent of input point subsets (as
recorded in the field named `"CGRIDID"`) is generated out of input
points.

## Usage

``` r
par_pad_balanced(points_in = NULL, ngroups, padding)
```

## Arguments

- points_in:

  `sf` or `SpatVector` object. Point geometries. Default is NULL.

- ngroups:

  integer(1). The number of groups.

- padding:

  numeric(1). A extrusion factor to make buffer to clip actual datasets.
  Depending on the length unit of the CRS of input.

## Value

A list of two,

- `original`: exhaustive and non-overlapping grid polygons in the class
  of input

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
[`par_pad_grid()`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md),
[`par_split_list()`](https://docs.ropensci.org/chopin/reference/par_split_list.md)

## Author

Insang Song

## Examples

``` r
lastpar <- par(mfrow = c(1, 1))
library(terra)
library(sf)
options(sf_use_s2 = FALSE)

ncpath <- system.file("gpkg/nc.gpkg", package = "sf")
nc <- terra::vect(ncpath)
nc_rp <- terra::spatSample(nc, 1000)

nc_gr <- par_pad_balanced(nc_rp, 10L, 1000)
nc_gr
#> $original
#> class       : SpatVector
#> geometry    : points
#> dimensions  : 1000, 15  (geometries, attributes)
#> extent      : -84.31143, -75.84704, 33.91155, 36.557  (xmin, xmax, ymin, ymax)
#> coord. ref. : lon/lat NAD27 (EPSG:4267)
#> names       :  AREA PERIMETER CNTY_ CNTY_ID      NAME  FIPS FIPSNO CRESS_ID BIR74 SID74   (and 5 more)
#> type        : <num>     <num> <num>   <num>     <chr> <chr>  <num>    <int> <num> <num>
#> values      : 0.212     2.024  2241    2241 Brunswick 37019  37019       10  2181     5
#>                0.12     1.686  2088    2088  Cherokee 37039  37039       20  1027     2
#>               0.121     1.855  2107    2107  Richmond 37153  37153       77  2756     4
#>               ...
#> 
#> $padded
#> class       : SpatVector
#> geometry    : polygons
#> dimensions  : 10, 1  (geometries, attributes)
#> extent      : -1084.311, 924.153, -966.0884, 1036.557  (xmin, xmax, ymin, ymax)
#> coord. ref. : 
#> names       : CGRIDID
#> type        :   <num>
#> values      :       1
#>                     2
#>                     3
#>               ...
#> 
par(lastpar)
```
