# Partition coordinates into quantile polygons

Partition coordinates into quantile polygons

## Usage

``` r
par_cut_coords(x = NULL, y = NULL, quantiles)
```

## Arguments

- x:

  numeric/sf/SpatVector. x-coordinates (if numeric).

- y:

  numeric. y-coordinates.

- quantiles:

  numeric vector. Quantiles.

## Value

A `SpatVector` object with field `CGRIDID`.

## Note

This function is only for two-dimensional points.

## See also

Other Parallelization:
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
[`par_pad_grid()`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md),
[`par_split_list()`](https://docs.ropensci.org/chopin/reference/par_split_list.md)
