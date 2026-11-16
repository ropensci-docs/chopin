# Check the subject object and perform necessary conversions if needed.

This function checks the class of the input object and performs
necessary conversions if needed.

## Usage

``` r
.check_vector(
  input,
  input_id = NULL,
  extent = NULL,
  out_class = character(1),
  ...
)
```

## Arguments

- input:

  sf/SpatVector/character. The input object to be checked.

- input_id:

  character(1). ID field of the subject object.

- extent:

  numeric(4). The extent of the subject object. Numeric vector should be
  put in order of `c(xmin, xmax, ymin, ymax)`.

- out_class:

  character(1). The class of the output object. Should be one of
  `c("sf", "terra")`.

- ...:

  Placeholder.

## Value

The checked and converted subject object.
