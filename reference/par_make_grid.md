# Generate grid polygons

Returns a sf object that includes x- and y- index by using two inputs
ncutsx and ncutsy, which are x- and y-directional splits, respectively.

## Usage

``` r
par_make_grid(points_in = NULL, ncutsx = NULL, ncutsy = NULL)
```

## Arguments

- points_in:

  `sf` or `SpatVector` object. Target points of computation.
  character(1) of file path is also acceptable.

- ncutsx:

  integer(1). The number of splits along x-axis.

- ncutsy:

  integer(1). The number of splits along y-axis.

## Value

A `sf` or `SpatVector` object of computation grids with unique grid id
(CGRIDID).

## Note

Grids are generated based on the extent of `points_in` first, then
exhaustive grids will be filtered by the intersection between these and
`points_in`. Thus, the number of generated grids may be smaller than
`ncutsx * ncutsy`.

## See also

Other Parallelization:
[`par_cut_coords()`](https://docs.ropensci.org/chopin/reference/par_cut_coords.md),
[`par_grid()`](https://docs.ropensci.org/chopin/reference/par_grid.md),
[`par_grid_mirai()`](https://docs.ropensci.org/chopin/reference/par_grid_mirai.md),
[`par_hierarchy()`](https://docs.ropensci.org/chopin/reference/par_hierarchy.md),
[`par_hierarchy_mirai()`](https://docs.ropensci.org/chopin/reference/par_hierarchy_mirai.md),
[`par_make_dggrid()`](https://docs.ropensci.org/chopin/reference/par_make_dggrid.md),
[`par_make_h3()`](https://docs.ropensci.org/chopin/reference/par_make_h3.md),
[`par_merge_grid()`](https://docs.ropensci.org/chopin/reference/par_merge_grid.md),
[`par_multirasters()`](https://docs.ropensci.org/chopin/reference/par_multirasters.md),
[`par_multirasters_mirai()`](https://docs.ropensci.org/chopin/reference/par_multirasters_mirai.md),
[`par_pad_balanced()`](https://docs.ropensci.org/chopin/reference/par_pad_balanced.md),
[`par_pad_grid()`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md),
[`par_split_list()`](https://docs.ropensci.org/chopin/reference/par_split_list.md)

## Author

Insang Song
