# Good practice of using \`chopin\`

## Assumptions

Users can access multi-threaded central processing units and sufficient
memory (i.e., at least 4GB/thread)

## Basic workflow

### Minimize errors

Consider using [`try()`](https://rdrr.io/r/base/try.html) or
[`tryCatch()`](https://rdrr.io/r/base/conditions.html) to let an error
will not halt all the work without results, especially when using
higher-level functions.

### Raster workflow: stacked vs file-based parallelization

> 
> ``` math
> !NOTE
> ```
> **It’s up to the users’ system specification and the size of the
> data.**

For faster computation, there are two strategies. One is parallelization
which is implemented in `chopin` for example. The other strategy is to
stack rasters and extract values at once. Adjustment of arguments in
[`exactextractr::exact_extract`](https://isciences.gitlab.io/exactextractr/reference/exact_extract.html)
will benefit many people who are dealing with sizable data that are able
to be stacked. We compare the two strategies in terms of computation
time.

Before proceeding, users need to consider the hardware specification.
For example, memory and storage to leverage the maximal performance of
`exact_extract`. Specifically speaking, memory capacity is crucial to
store the stacked rasters in memory rather than to read the proxy of
rasters from the disk as implemented in `terra`. `max_cells_in_memory`
is a key argument to control the memory usage. The maximum possible
value for this argument is $`2^{31} - 1 = 2,147,483,647`$, roughly
`2.147e9`, as applied in the example above. As memory bandwidth is much
faster than disk input/output specification, the stacked rasters with
high `max_cells_in_memory` applied will run faster than file-based
parallelization or the extraction with lower value of
`max_cells_in_memory`. The performance does not come without a cost. The
memory-intensive setting is not suitable for the system with limited
memory, for example, in consumer laptops with around 16 GB of RAM.

> 
> ``` math
> !NOTE
> ```
> Stacking rasters usually takes the large amount of memory. Users need
> to consider the memory capacity of the system before stacking rasters.

### Raster-Vector overlay

Use
[`chopin::par_pad_grid()`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md)
to make exhaustive (fully filling the entire extent without any gaps)
and padded grid objects in `SpatVector` class.
[`chopin::par_grid()`](https://docs.ropensci.org/chopin/reference/par_grid.md)
filters features that **intersect** with each grid. Post-processing is
necessary as polygons may be taken more than twice in parallelized
calculation. If users have a field containing divisible and hierarchical
information, dataset can be processed with
[`chopin::par_hierarchy()`](https://docs.ropensci.org/chopin/reference/par_hierarchy.md).

### Customization

`chopin` way to parallelize is to iterate calculation over a list then
to use [`future.apply`](https://CRAN.R-project.org/package=future.apply)
function. If one wants to customize parallelization workflow like
`chopin`, consider making list objects in two steps. First, assign
extent vectors to each element. In this case, users should be aware of
the coordinate system of both the input data and the coordinates of the
extent vectors. Second, use preprocessed vector objects with respect to
each extent vector in the first list. We recommend to name each list
element to debug easily. For
[`par_multirasters()`](https://docs.ropensci.org/chopin/reference/par_multirasters.md)
workflow, using file or database table paths is recommended instead of
`SpatRaster` objects in `future` parallel processing workflow. This is
because `SpatRaster` objects made from external files are Rcpp pointers,
which cannot be exported to processes in parallelization.

### More tips to save time and memory

1.  Read vector data with `sf` package at first. It is a bit faster than
    `terra` and will save time for processing as `exactextractr` is
    designed to work with `sf` objects for vector inputs.
2.  If your analysis does not require the high precision of vector data,
    simplification of geometries (e.g., using
    [`rmapshaper`](https://github.com/ateucher/rmapshaper), i.e.,
    `ms_simplify`) will result in considerable time savings.

### Save computing costs

If HPC systems are used, you will have CPU-memory usage quota per user.
Thus, it is always recommended to test your computation workflow with
subsets in manageable sizes. It can give you an estimate for the total
computational demand.
[`profvis::profvis()`](https://profvis.r-lib.org/reference/profvis.html)
helps to summarize runtime per function call and memory usage. For
testing, use `future::plan(future::sequential)` to see single-thread.
Keep
[`chopin::par_pad_grid()`](https://docs.ropensci.org/chopin/reference/par_pad_grid.md)
arguments the same then only take one or two grids using `lapply`.
Preferably the hardest case will be tested to estimate the maximum peak
memory usage per thread. After prototyping, take the numbers as
reference numbers to submit a job to HPC with a proper amount of
computational assets: each HPC management system offers tools to see
statistics and task summaries. Users are advised to use these records to
plan asset allocation for the next time.
