# Return the package the input object is based on

Detect whether the input object is sf or Spat\* object.

## Usage

``` r
dep_check(input)
```

## Arguments

- input:

  Spat\* in terra or sf object.

## Value

A character object; one of `"character"`, `"terra"` and `"sf"`

## See also

Other Helper functions:
[`datamod()`](https://docs.ropensci.org/chopin/reference/datamod.md),
[`dep_switch()`](https://docs.ropensci.org/chopin/reference/dep_switch.md),
[`get_clip_ext()`](https://docs.ropensci.org/chopin/reference/get_clip_ext.md),
[`par_def_q()`](https://docs.ropensci.org/chopin/reference/par_def_q.md),
[`reproject_std()`](https://docs.ropensci.org/chopin/reference/reproject_std.md),
[`reproject_to_raster()`](https://docs.ropensci.org/chopin/reference/reproject_to_raster.md)

## Author

Insang Song
