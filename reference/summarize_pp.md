# Point to polygon summary using target polygons and source points

When `x` and `y` are different classes, `y` will be converted to the
class of `x`.

## Usage

``` r
summarize_pp(x, y, ...)

# S4 method for class 'SpatVector'
summarize_pp(
  x,
  y,
  target_fields = NULL,
  id_x = "ID",
  fun = mean,
  extent = NULL,
  ...
)

# S4 method for class 'character'
summarize_pp(
  x,
  y,
  target_fields = NULL,
  id_x = "ID",
  fun = mean,
  out_class = "terra",
  extent = NULL,
  ...
)

# S4 method for class 'sf'
summarize_pp(
  x,
  y,
  target_fields = NULL,
  id_x = "ID",
  fun = mean,
  extent = NULL,
  ...
)
```

## Arguments

- x:

  A sf/SpatVector object or file path of polygons to which point
  attributes will be summarized.

- y:

  A sf/SpatVector object or file path of points from which summaries
  will be calculated.

- ...:

  Additional arguments passed to `fun`.

- target_fields:

  character. Field names in `y` to summarize.

- id_x:

  character(1). The unique identifier of each polygon in `x`. Default is
  `"ID"`.

- fun:

  function(1)/character(1). The function to calculate the point summary.
  Default is
  [`mean`](https://rspatial.github.io/terra/reference/summarize-generics.html).
  Character input should be a summary function accepted by
  [`match.fun()`](https://rdrr.io/r/base/match.fun.html).

- extent:

  numeric(4) or SpatExtent object. Extent of clipping `x`. It only works
  with `x` of character(1) file path. See
  [`terra::ext`](https://rspatial.github.io/terra/reference/ext.html)
  for more details. Coordinate systems should match.

- out_class:

  character(1). "sf" or "terra". Output class.

## Value

If `x` is `sf`, returns an `sf` object with summarized fields joined to
polygons. Otherwise returns a `data.frame`.

## Note

`x` should contain polygon geometries and `y` should contain point
geometries.

## See also

Other Macros for calculation:
[`extract_at()`](https://docs.ropensci.org/chopin/reference/extract_at.md),
[`kernelfunction()`](https://docs.ropensci.org/chopin/reference/kernelfunction.md),
[`summarize_aw()`](https://docs.ropensci.org/chopin/reference/summarize_aw.md),
[`summarize_sedc()`](https://docs.ropensci.org/chopin/reference/summarize_sedc.md),
[`summarize_st()`](https://docs.ropensci.org/chopin/reference/summarize_st.md)

## Author

Insang Song <geoissong@gmail.com>
