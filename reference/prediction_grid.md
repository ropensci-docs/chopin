# Regular grid points in the mainland United States at 1km spatial resolution

Regular grid points in the mainland United States at 1km spatial
resolution

## Usage

``` r
prediction_grid
```

## Format

A data frame with 8,092,995 rows and three variables:

- site_id:

  Unique point identifier. Arbitrarily generated.

- lon:

  Longitude

- lat:

  Latitude

## Source

Mainland United States polygon was obtained from the US Census Bureau.

## Note

Coordinates are in EPSG:5070 (Conus Albers Equal Area)

## See also

Other Dataset:
[`ncpoints`](https://docs.ropensci.org/chopin/reference/ncpoints.md)

## Examples

``` r
data("prediction_grid", package = "chopin")
```
