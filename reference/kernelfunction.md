# Kernel functions

Kernel functions

## Usage

``` r
kernelfunction(
  d,
  bw,
  kernel = c("uniform", "quartic", "triweight", "epanechnikov")
)
```

## Arguments

- d:

  Distance

- bw:

  Bandwidth of a kernel

- kernel:

  Kernel type. One of `"uniform"`, `"quartic"`, `"triweight"`, and
  `"epanechnikov"`

## Value

numeric. Kernel weights.

## References

https://github.com/JanCaha/SpatialKDE

## See also

Other Macros for calculation:
[`extract_at()`](https://docs.ropensci.org/chopin/reference/extract_at.md),
[`summarize_aw()`](https://docs.ropensci.org/chopin/reference/summarize_aw.md),
[`summarize_pp()`](https://docs.ropensci.org/chopin/reference/summarize_pp.md),
[`summarize_sedc()`](https://docs.ropensci.org/chopin/reference/summarize_sedc.md),
[`summarize_st()`](https://docs.ropensci.org/chopin/reference/summarize_st.md)

## Examples

``` r
v_dist <- c(1, 10, 100, 25, 50, 0.1)
bw_dist1 <- 1
bw_dist2 <- 10
kernelfunction(v_dist, bw_dist1, "uniform")
#> [1] 0.5 0.0 0.0 0.0 0.0 0.5
kernelfunction(v_dist, bw_dist1, "quartic")
#> [1] 0.0000000 0.0000000 0.0000000 0.0000000 0.0000000 0.9188437
kernelfunction(v_dist, bw_dist1, "triweight")
#> [1] 0.000 0.000 0.000 0.000 0.000 0.999
kernelfunction(v_dist, bw_dist1, "epanechnikov")
#> [1] 0.0000 0.0000 0.0000 0.0000 0.0000 0.7425
kernelfunction(v_dist, bw_dist2, "uniform")
#> [1] 0.5 0.5 0.0 0.0 0.0 0.5
kernelfunction(v_dist, bw_dist2, "quartic")
#> [1] 0.9188437 0.0000000 0.0000000 0.0000000 0.0000000 0.9373125
kernelfunction(v_dist, bw_dist2, "triweight")
#> [1] 0.999000 0.000000 0.000000 0.000000 0.000000 0.999999
kernelfunction(v_dist, bw_dist2, "epanechnikov")
#> [1] 0.742500 0.000000 0.000000 0.000000 0.000000 0.749925
```
