# Map specified arguments to others in literals

This function creates a new function that wraps an existing function,
remapping the argument names based on a user-specified literal mapping.
Specifically, arguments passed to the new function with names on the
left-hand side of the mapping are renamed to the corresponding
right-hand side names before being passed to the original function.
Users map two arguments without `x` and/or `y` to standardize the
argument names, x and y, to the target function. This function is
particularly useful to parallelize functions for spatial data outside
`sf` and `terra` packages that do not have arguments named x and/or y.
`par_*` functions could detect such functions by wrapping
nonstandardized functions to parallelize the computation.

## Usage

``` r
par_convert_f(fun, ...)
```

## Arguments

- fun:

  A function to be wrapped.

- ...:

  A set of named arguments representing the mapping from the new
  function's argument names (left-hand side) to the original function's
  argument names (right-hand side). For example, `x = group, y = score`
  maps argument `x` to `group` and `y` to `score`.

## Value

A new function that accepts the remapped arguments and calls the
original function.

## Examples

``` r
# Define an original function that expects arguments 'group' and 'score'
original_fun <- function(group, score, home = FALSE) {
  list(group = group, score = score, home = home)
}

# Create a new function that maps 'x' to 'group' and 'y' to 'score'
new_fun <- par_convert_f(original_fun, x = group, y = score)

# Call the new function using the new argument names
result <- new_fun(x = 10, y = 20)
print(result)
#> $group
#> [1] 10
#> 
#> $score
#> [1] 20
#> 
#> $home
#> [1] FALSE
#> 
```
