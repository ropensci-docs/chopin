# Generate groups based on balanced clustering

For balancing computational loads, the function uses the `anticlust`
package to cluster the input points. The number of clusters is
determined by the `num_cluster` argument. Each cluster will have equal
number of points. Grids will be generated based on the cluster extents.
At the lower level, the function uses
[`terra::distance()`](https://rspatial.github.io/terra/reference/distance.html)
function to calculate the Euclidean distance between points.

## Usage

``` r
par_make_balanced(points_in = NULL, n_clusters = NULL)
```

## Arguments

- points_in:

  `sf` or `SpatVector` object. Target points of computation.

- n_clusters:

  integer(1). The number of clusters.

## Value

`SpatVector` object with a field `"CGRIDID"`.

## Note

This function is only for two-dimensional points. The results will be
irregular grids with or without overlapping parts.

## Author

Insang Song
