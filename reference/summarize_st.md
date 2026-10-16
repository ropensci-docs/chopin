# Summarize Data by Time or Space

Summarize numeric columns of a data frame by either time intervals or
spatial features. This function supports two modes of operation:
time-based grouping and spatial grouping.

## Usage

``` r
summarize_st(df, f, id_col, .by, ...)
```

## Arguments

- df:

  A data frame, sf object, or SpatVector containing the data to
  summarize.

- f:

  A function to apply for summarization (e.g., "mean", "sum").

- id_col:

  A column name or expression specifying the ID column for grouping.

- .by:

  Either a character string specifying a time unit ("minutes", "hours",
  "days", "weeks", "months", "quarters", "years") for temporal grouping,
  or an sf/SpatVector object for spatial grouping.

- ...:

  Additional arguments passed to
  [`summarize_aw()`](https://docs.ropensci.org/chopin/reference/summarize_aw.md)
  when doing spatial grouping.

## Value

A data frame with summarized values. For time-based grouping, includes
the original time column and grouped summaries. For spatial grouping,
returns the result from
[`summarize_aw()`](https://docs.ropensci.org/chopin/reference/summarize_aw.md).

## Details

When `.by` is a character string, the function performs time-based
summarization: it groups data by ID and time intervals, then applies the
summarization function to all numeric columns.

When `.by` is an sf or SpatVector object, the function performs spatial
summarization using
[`summarize_aw()`](https://docs.ropensci.org/chopin/reference/summarize_aw.md)
to aggregate data across spatial features.

## Note

For time-based grouping, the function expects exactly one time column in
the data frame. The time values will be "floored" to the nearest time
unit defined by `.by` for grouping.

## See also

Other Macros for calculation:
[`extract_at()`](https://docs.ropensci.org/chopin/reference/extract_at.md),
[`kernelfunction()`](https://docs.ropensci.org/chopin/reference/kernelfunction.md),
[`summarize_aw()`](https://docs.ropensci.org/chopin/reference/summarize_aw.md),
[`summarize_pp()`](https://docs.ropensci.org/chopin/reference/summarize_pp.md),
[`summarize_sedc()`](https://docs.ropensci.org/chopin/reference/summarize_sedc.md)
