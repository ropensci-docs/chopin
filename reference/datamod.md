# Return the input's GIS data model type

This function returns one of 'vector' or 'raster' depending on the input
class.

## Usage

``` r
datamod(input)
```

## Arguments

- input:

  Spat\*/sf/stars object.

## Value

character(1). One of `"vector"` or `"raster"`.

## Note

Although `stars` object is a little ambiguous whether to classify vector
or raster, it will be considered raster in this package.

## See also

Other Helper functions:
[`dep_check()`](https://docs.ropensci.org/chopin/reference/dep_check.md),
[`dep_switch()`](https://docs.ropensci.org/chopin/reference/dep_switch.md),
[`get_clip_ext()`](https://docs.ropensci.org/chopin/reference/get_clip_ext.md),
[`par_def_q()`](https://docs.ropensci.org/chopin/reference/par_def_q.md),
[`reproject_std()`](https://docs.ropensci.org/chopin/reference/reproject_std.md),
[`reproject_to_raster()`](https://docs.ropensci.org/chopin/reference/reproject_to_raster.md)

## Author

Insang Song
