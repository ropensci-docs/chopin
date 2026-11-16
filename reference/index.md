# Package index

## Parallelization core functions

Functions for parallelizing `chopin`, `terra` and `sf` geospatial
operations

- [`par_grid()`](https://docs.ropensci.org/chopin/reference/par_grid.md)
  : Parallelize spatial computation over the computational grids
- [`par_hierarchy()`](https://docs.ropensci.org/chopin/reference/par_hierarchy.md)
  : Parallelize spatial computation by hierarchy in input data
- [`par_multirasters()`](https://docs.ropensci.org/chopin/reference/par_multirasters.md)
  : Parallelize spatial computation over multiple raster files
- [`par_grid_mirai()`](https://docs.ropensci.org/chopin/reference/par_grid_mirai.md)
  : Parallelize spatial computation over the computational grids
- [`par_hierarchy_mirai()`](https://docs.ropensci.org/chopin/reference/par_hierarchy_mirai.md)
  : Parallelize spatial computation by hierarchy in input data
- [`par_multirasters_mirai()`](https://docs.ropensci.org/chopin/reference/par_multirasters_mirai.md)
  : Parallelize spatial computation over multiple raster files

## `par_grid` preparation functions

Regular grid or equal number partitioning to run `par_grid`

- [`par_pad_grid()`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md)
  : Get a set of computational grids
- [`par_pad_balanced()`](https://docs.ropensci.org/chopin/reference/par_pad_balanced.md)
  : Extension of par_make_balanced for padded grids
- [`par_make_h3()`](https://docs.ropensci.org/chopin/reference/par_make_h3.md)
  : Convert a input sf object to H3 hexagons
- [`par_make_dggrid()`](https://docs.ropensci.org/chopin/reference/par_make_dggrid.md)
  : Convert DGGRID indices to sf object
- [`par_merge_grid()`](https://docs.ropensci.org/chopin/reference/par_merge_grid.md)
  : Merge adjacent grid polygons with given rules
- [`par_split_list()`](https://docs.ropensci.org/chopin/reference/par_split_list.md)
  : Split grid list to a nested list of row-wise data frames

## chopin calculation functions

Functions for streamlining oft-used operations

- [`extract_at()`](https://docs.ropensci.org/chopin/reference/extract_at.md)
  : Extract raster values with point buffers or polygons
- [`summarize_aw()`](https://docs.ropensci.org/chopin/reference/summarize_aw.md)
  : Area weighted summary using two polygon objects
- [`summarize_pp()`](https://docs.ropensci.org/chopin/reference/summarize_pp.md)
  : Point to polygon summary using target polygons and source points
- [`summarize_sedc()`](https://docs.ropensci.org/chopin/reference/summarize_sedc.md)
  : Calculate Sum of Exponentially Decaying Contributions (SEDC)
  covariates
- [`summarize_st()`](https://docs.ropensci.org/chopin/reference/summarize_st.md)
  : Summarize Data by Time or Space

## Helper function

Parallelization helper function

- [`par_convert_f()`](https://docs.ropensci.org/chopin/reference/par_convert_f.md)
  : Map specified arguments to others in literals

## Dataset

Bundled data in chopin package

- [`prediction_grid`](https://docs.ropensci.org/chopin/reference/prediction_grid.md)
  : Regular grid points in the mainland United States at 1km spatial
  resolution
- [`ncpoints`](https://docs.ropensci.org/chopin/reference/ncpoints.md) :
  Mildly clustered points in North Carolina, United States
