# Align vector CRS to raster's

Align vector CRS to raster's

## Usage

``` r
reproject_to_raster(vector = NULL, raster = NULL)
```

## Arguments

- vector:

  `sf`/`stars`/`SpatVector`/`SpatRaster` object

- raster:

  `SpatRaster` object

## Value

Reprojected object in the same class as `vector`

## See also

Other Helper functions:
[`datamod()`](https://docs.ropensci.org/chopin/reference/datamod.md),
[`dep_check()`](https://docs.ropensci.org/chopin/reference/dep_check.md),
[`dep_switch()`](https://docs.ropensci.org/chopin/reference/dep_switch.md),
[`get_clip_ext()`](https://docs.ropensci.org/chopin/reference/get_clip_ext.md),
[`par_def_q()`](https://docs.ropensci.org/chopin/reference/par_def_q.md),
[`reproject_std()`](https://docs.ropensci.org/chopin/reference/reproject_std.md)

## Author

Insang Song
