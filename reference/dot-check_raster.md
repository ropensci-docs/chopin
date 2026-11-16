# Check Raster Input

This function checks the input object to ensure it is a valid raster
object or a character path to a raster file. It also provides warnings
and informative messages based on the input type.

## Usage

``` r
.check_raster(input, extent = NULL, ...)
```

## Arguments

- input:

  The input object to be checked. It can be either a SpatRaster object
  or a character path to a raster file.

- extent:

  The extent of the raster. Defaults to NULL. Numeric vector should be
  put in order of `c(xmin, xmax, ymin, ymax)`.

- ...:

  Placeholder.

## Value

The validated input object.
