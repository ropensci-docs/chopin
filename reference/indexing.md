# Subset for nonidentical package class objects

Subset for nonidentical package class objects

Intersect different data model objects

## Usage

``` r
# S4 method for class 'SpatVector,bbox,missing,ANY'
x[i, j]

# S4 method for class 'SpatVector,sf,missing,ANY'
x[i, j]

# S4 method for class 'SpatVector,sfc,missing,ANY'
x[i, j]

# S4 method for class 'SpatVector,SpatExtent,missing,ANY'
x[i, j]

.intersect(x, y)
```

## Arguments

- x:

  SpatVector/sf/SpatRaster object to be intersected.

- i:

  Dataset used to subset x.

- j:

  Column indices or names.

- y:

  SpatVector/sf object. Intersecting object.
