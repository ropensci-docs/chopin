# Check coordinate system then reproject

The input is checked whether its coordinate system is present. If not,
it is reprojected to the CRS specified in `crs_standard`.

## Usage

``` r
reproject_std(input, crs_standard = "EPSG:4326")
```

## Arguments

- input:

  Input object one of sf or terra::Spat\* object

- crs_standard:

  character(1). A standard definition of coordinate reference system.
  Default is `"EPSG:4326"` Consult [epsg.io](https://epsg.io) for
  details of other CRS.

## Value

A (reprojected) `sf` or `SpatVector` object.

## Note

This function works well with EPSG codes.

## See also

Other Helper functions:
[`datamod()`](https://docs.ropensci.org/chopin/reference/datamod.md),
[`dep_check()`](https://docs.ropensci.org/chopin/reference/dep_check.md),
[`dep_switch()`](https://docs.ropensci.org/chopin/reference/dep_switch.md),
[`get_clip_ext()`](https://docs.ropensci.org/chopin/reference/get_clip_ext.md),
[`par_def_q()`](https://docs.ropensci.org/chopin/reference/par_def_q.md),
[`reproject_to_raster()`](https://docs.ropensci.org/chopin/reference/reproject_to_raster.md)

## Author

Insang Song
