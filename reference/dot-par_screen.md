# Prescreen input data for parallelization

This function takes input object and type character to ingest the input
object to return the object in the desired class.

## Usage

``` r
.par_screen(type, input, input_id = NULL, out_class = "terra", .window = NULL)
```

## Arguments

- type:

  character(1). "raster" or "vector".

- input:

  object. Input object.

- input_id:

  character(1). Default is NULL. If NULL, the function will not check
  the object with an ID column.

- out_class:

  character(1). Default is NULL, but should be one of
  `c("sf", "terra")`. Default is "terra".

- .window:

  numeric(4)/SpatExtent/st_bbox object. Loading window.
