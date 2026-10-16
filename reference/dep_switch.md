# Switch spatial data class

Convert class between `sf`/`stars`-`terra`

## Usage

``` r
dep_switch(input)
```

## Arguments

- input:

  Spat\* in terra or sf object.

## Value

Data converted to the other package class (if sf, terra; if terra, sf)

## See also

Other Helper functions:
[`datamod()`](https://docs.ropensci.org/chopin/reference/datamod.md),
[`dep_check()`](https://docs.ropensci.org/chopin/reference/dep_check.md),
[`get_clip_ext()`](https://docs.ropensci.org/chopin/reference/get_clip_ext.md),
[`par_def_q()`](https://docs.ropensci.org/chopin/reference/par_def_q.md),
[`reproject_std()`](https://docs.ropensci.org/chopin/reference/reproject_std.md),
[`reproject_to_raster()`](https://docs.ropensci.org/chopin/reference/reproject_to_raster.md)

## Author

Insang Song
