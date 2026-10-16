# Calculate Sum of Exponentially Decaying Contributions (SEDC) covariates

Calculate Sum of Exponentially Decaying Contributions (SEDC) covariates

## Usage

``` r
summarize_sedc(
  point_from = NULL,
  point_to = NULL,
  id = NULL,
  sedc_bandwidth = NULL,
  threshold = NULL,
  target_fields = NULL,
  extent_from = NULL,
  extent_to = NULL,
  ...
)
```

## Arguments

- point_from:

  `SpatVector` object. Locations where the sum of SEDCs are calculated.

- point_to:

  `SpatVector` object. Locations where each SEDC is calculated.

- id:

  character(1). Name of the unique id field in `point_to`.

- sedc_bandwidth:

  numeric(1). Distance at which the source concentration is reduced to
  `exp(-3)` (approximately -95 %)

- threshold:

  numeric(1). For computational efficiency, the nearest points in
  threshold will be selected. `2 * sedc_bandwidth` is applied if this
  value remains `NULL`.

- target_fields:

  character. Field names to calculate SEDC.

- extent_from:

  numeric(4) or SpatExtent. Extent of clipping `point_from`. It only
  works with `point_from` of character(1) file path. See
  [`terra::ext`](https://rspatial.github.io/terra/reference/ext.html)
  for more details. Coordinate systems should match.

- extent_to:

  numeric(4) or SpatExtent. Extent of clipping `point_to`.

- ...:

  Placeholder.

## Value

data.frame object with input field names with a suffix `"_sedc"` where
the sums of EDC are stored. Additional attributes are attached for the
EDC information.

- `attr(result, "sedc_bandwidth")`: the bandwidth where concentration
  reduces to approximately five percent

- `attr(result, "sedc_threshold")`: the threshold distance at which
  emission source points are excluded beyond that

## Details

The SEDC is specialized in vector to vector summary of covariates with
exponential decay. Decaying slope will be defined by `sedc_bandwidth`,
where the concentration of the source is reduced to \$\exp(-3)\$
(approximately 5 \\ of the attenuating concentration with the distance
from the sources. It can be thought of as a fixed bandwidth kernel
weighted sum of covariates, which encapsulates three steps:

- Calculate the distance between each source and target points.

- Calculate the weight of each source point with the exponential decay.

- Summarize the weighted covariates.

## Note

Distance calculation is done with `terra` functions internally. Thus,
the function internally converts `sf` objects in `point_*` arguments to
`terra`. Please note that any `NA` values in the input will be ignored
in SEDC calculation.

## References

- Messier KP, Akita Y, Serre ML. (2012). Integrating Address Geocoding,
  Land Use Regression, and Spatiotemporal Geostatistical Estimation for
  Groundwater Tetrachloroethylene. *Environmental Science & Technology*
  46(5),
  2772-2780.([doi:10.1021/es203152a](https://doi.org/10.1021/es203152a)
  )

- Wiesner C. (n.d.). Euclidean Sum of Exponentially Decaying
  Contributions Tutorial.

## See also

Other Macros for calculation:
[`extract_at()`](https://docs.ropensci.org/chopin/reference/extract_at.md),
[`kernelfunction()`](https://docs.ropensci.org/chopin/reference/kernelfunction.md),
[`summarize_aw()`](https://docs.ropensci.org/chopin/reference/summarize_aw.md),
[`summarize_pp()`](https://docs.ropensci.org/chopin/reference/summarize_pp.md),
[`summarize_st()`](https://docs.ropensci.org/chopin/reference/summarize_st.md)

## Author

Insang Song

## Examples

``` r
library(terra)
library(sf)
set.seed(101)
ncpath <- system.file("gpkg/nc.gpkg", package = "sf")
nc <- terra::vect(ncpath)
nc <- terra::project(nc, "EPSG:5070")
pnt_from <- terra::centroids(nc, inside = TRUE)
pnt_from <- pnt_from[, "NAME"]
pnt_to <- terra::spatSample(nc, 100L)
pnt_to$pid <- seq(1, 100)
pnt_to <- pnt_to[, "pid"]
pnt_to$val1 <- rgamma(100L, 1, 0.05)
pnt_to$val2 <- rgamma(100L, 2, 1)

vals <- c("val1", "val2")
suppressWarnings(
  summarize_sedc(pnt_from, pnt_to, "NAME", 1e5, 2e5, vals)
)
#> # A tibble: 100 × 3
#>    NAME      val1_sedc val2_sedc
#>    <chr>         <dbl>     <dbl>
#>  1 Alamance       66.3      6.71
#>  2 Alexander      98.0     11.7 
#>  3 Alleghany     135.       8.57
#>  4 Anson          50.0      5.23
#>  5 Ashe          143.       8.42
#>  6 Avery          76.1      8.76
#>  7 Beaufort       58.8      6.29
#>  8 Bertie         70.1      7.85
#>  9 Bladen         79.5      6.82
#> 10 Brunswick      54.3      5.34
#> # ℹ 90 more rows
```
