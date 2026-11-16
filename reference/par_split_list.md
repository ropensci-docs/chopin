# Split grid list to a nested list of row-wise data frames

Split grid list to a nested list of row-wise data frames

## Usage

``` r
par_split_list(gridlist)
```

## Arguments

- gridlist:

  list. Output of
  [`par_pad_grid`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md)
  or
  [`par_pad_balanced`](https://docs.ropensci.org/chopin/reference/par_pad_balanced.md)

## Value

A nested list of data frames or WKT strings.

## Details

If the input is a data frame, the function will return a list of two
data frames: `original` and `padded`. If the input is a WKT vector, the
function will return a list of two WKT strings: `original` and `padded`.

## See also

Other Parallelization:
[`par_cut_coords()`](https://docs.ropensci.org/chopin/reference/par_cut_coords.md),
[`par_grid()`](https://docs.ropensci.org/chopin/reference/par_grid.md),
[`par_grid_mirai()`](https://docs.ropensci.org/chopin/reference/par_grid_mirai.md),
[`par_hierarchy()`](https://docs.ropensci.org/chopin/reference/par_hierarchy.md),
[`par_hierarchy_mirai()`](https://docs.ropensci.org/chopin/reference/par_hierarchy_mirai.md),
[`par_make_dggrid()`](https://docs.ropensci.org/chopin/reference/par_make_dggrid.md),
[`par_make_grid()`](https://docs.ropensci.org/chopin/reference/par_make_grid.md),
[`par_make_h3()`](https://docs.ropensci.org/chopin/reference/par_make_h3.md),
[`par_merge_grid()`](https://docs.ropensci.org/chopin/reference/par_merge_grid.md),
[`par_multirasters()`](https://docs.ropensci.org/chopin/reference/par_multirasters.md),
[`par_multirasters_mirai()`](https://docs.ropensci.org/chopin/reference/par_multirasters_mirai.md),
[`par_pad_balanced()`](https://docs.ropensci.org/chopin/reference/par_pad_balanced.md),
[`par_pad_grid()`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md)

## Examples

``` r
lastpar <- par(mfrow = c(1, 1))

library(sf)
library(terra)
options(sf_use_s2 = FALSE)

ncpath <- system.file("shape/nc.shp", package = "sf")
nc <- read_sf(ncpath)
nc <- st_transform(nc, "EPSG:5070")
nc_comp_region <-
  par_pad_grid(
    nc,
    mode = "grid",
    nx = 4L, ny = 2L,
    padding = 10000
  )
#> Switch sf class to terra...
#> Switch terra class to sf...
par_split_list(nc_comp_region)
#> [[1]]
#> [[1]]$original
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1054293 ymin: 1348021 xmax: 1249094 ymax: 1518623
#> Projected CRS: NAD83 / Conus Albers
#>                         geometry CGRIDID
#> 1 POLYGON ((1054293 1348021, ...       1
#> 
#> [[1]]$padded
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1044293 ymin: 1338021 xmax: 1259094 ymax: 1528623
#> Projected CRS: NAD83 / Conus Albers
#>   CGRIDID                       geometry
#> 1       1 POLYGON ((1044293 1338021, ...
#> 
#> 
#> [[2]]
#> [[2]]$original
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1249094 ymin: 1348021 xmax: 1443895 ymax: 1518623
#> Projected CRS: NAD83 / Conus Albers
#>                         geometry CGRIDID
#> 2 POLYGON ((1249094 1348021, ...       2
#> 
#> [[2]]$padded
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1239094 ymin: 1338021 xmax: 1453895 ymax: 1528623
#> Projected CRS: NAD83 / Conus Albers
#>   CGRIDID                       geometry
#> 2       2 POLYGON ((1239094 1338021, ...
#> 
#> 
#> [[3]]
#> [[3]]$original
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1443895 ymin: 1348021 xmax: 1638695 ymax: 1518623
#> Projected CRS: NAD83 / Conus Albers
#>                         geometry CGRIDID
#> 3 POLYGON ((1443895 1348021, ...       3
#> 
#> [[3]]$padded
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1433895 ymin: 1338021 xmax: 1648695 ymax: 1528623
#> Projected CRS: NAD83 / Conus Albers
#>   CGRIDID                       geometry
#> 3       3 POLYGON ((1433895 1338021, ...
#> 
#> 
#> [[4]]
#> [[4]]$original
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1638695 ymin: 1348021 xmax: 1833496 ymax: 1518623
#> Projected CRS: NAD83 / Conus Albers
#>                         geometry CGRIDID
#> 4 POLYGON ((1638695 1348021, ...       4
#> 
#> [[4]]$padded
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1628695 ymin: 1338021 xmax: 1843496 ymax: 1528623
#> Projected CRS: NAD83 / Conus Albers
#>   CGRIDID                       geometry
#> 4       4 POLYGON ((1628695 1338021, ...
#> 
#> 
#> [[5]]
#> [[5]]$original
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1054293 ymin: 1518623 xmax: 1249094 ymax: 1689226
#> Projected CRS: NAD83 / Conus Albers
#>                         geometry CGRIDID
#> 5 POLYGON ((1054293 1518623, ...       5
#> 
#> [[5]]$padded
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1044293 ymin: 1508623 xmax: 1259094 ymax: 1699226
#> Projected CRS: NAD83 / Conus Albers
#>   CGRIDID                       geometry
#> 5       5 POLYGON ((1044293 1508623, ...
#> 
#> 
#> [[6]]
#> [[6]]$original
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1249094 ymin: 1518623 xmax: 1443895 ymax: 1689226
#> Projected CRS: NAD83 / Conus Albers
#>                         geometry CGRIDID
#> 6 POLYGON ((1249094 1518623, ...       6
#> 
#> [[6]]$padded
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1239094 ymin: 1508623 xmax: 1453895 ymax: 1699226
#> Projected CRS: NAD83 / Conus Albers
#>   CGRIDID                       geometry
#> 6       6 POLYGON ((1239094 1508623, ...
#> 
#> 
#> [[7]]
#> [[7]]$original
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1443895 ymin: 1518623 xmax: 1638695 ymax: 1689226
#> Projected CRS: NAD83 / Conus Albers
#>                         geometry CGRIDID
#> 7 POLYGON ((1443895 1518623, ...       7
#> 
#> [[7]]$padded
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1433895 ymin: 1508623 xmax: 1648695 ymax: 1699226
#> Projected CRS: NAD83 / Conus Albers
#>   CGRIDID                       geometry
#> 7       7 POLYGON ((1433895 1508623, ...
#> 
#> 
#> [[8]]
#> [[8]]$original
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1638695 ymin: 1518623 xmax: 1833496 ymax: 1689226
#> Projected CRS: NAD83 / Conus Albers
#>                         geometry CGRIDID
#> 8 POLYGON ((1638695 1518623, ...       8
#> 
#> [[8]]$padded
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1628695 ymin: 1508623 xmax: 1843496 ymax: 1699226
#> Projected CRS: NAD83 / Conus Albers
#>   CGRIDID                       geometry
#> 8       8 POLYGON ((1628695 1508623, ...
#> 
#> 

par(lastpar)
```
