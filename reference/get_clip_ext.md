# Setting the clipping extent

Return clipping extent with buffer radius. It assumes the input CRS is
projected and linear unit is meters.

## Usage

``` r
get_clip_ext(pnts, radius, extrusion = 1.1)
```

## Arguments

- pnts:

  One of sf or SpatVector object. Target points of computation.

- radius:

  numeric(1). Buffer radius. It is assumed to be in meters

- extrusion:

  numeric(1). The extent extrusion factor. Default is 1.1, meaning that
  the actual padding is 10 percent wider than `radius`.

## Value

A [`terra::ext`](https://rspatial.github.io/terra/reference/ext.html) or
sfc_POLYGON object of the computation extent.

## See also

Other Helper functions:
[`datamod()`](https://docs.ropensci.org/chopin/reference/datamod.md),
[`dep_check()`](https://docs.ropensci.org/chopin/reference/dep_check.md),
[`dep_switch()`](https://docs.ropensci.org/chopin/reference/dep_switch.md),
[`par_def_q()`](https://docs.ropensci.org/chopin/reference/par_def_q.md),
[`reproject_std()`](https://docs.ropensci.org/chopin/reference/reproject_std.md),
[`reproject_to_raster()`](https://docs.ropensci.org/chopin/reference/reproject_to_raster.md)

## Author

Insang Song
