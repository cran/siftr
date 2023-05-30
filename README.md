<!-- badges: start -->
[![CRAN status](https://www.r-pkg.org/badges/version/siftr)](https://CRAN.R-project.org/package=siftr)
<!-- badges: end -->

# Siftr

If you work as an analyst, you probably shift projects often and need to get oriented in a new dataset quickly. `siftr` is an interactive tool that helps you find the column you need in a large dataframe using powerful 'fuzzy' searches.

It was designed with medical, census, and survey data in mind, where dataframes can reach hundreds of columns and millions of rows.

# Installation

``` r
# CRAN release pending

# Live development version from Github
remotes::install_github("DesiQuintans/siftr")
```

# Functions in `siftr`

| Function         | Description                                          |
|:-----------------|:-----------------------------------------------------|
| `sift()`         | Search through a dataframe's columns.                |
| `save_dictionary()` | Save the data dictionary for use with [`tsv2label`][1] |
| `options_sift()` | Get and set options related to how `sift` functions. |
| `mtcars_lab`     | A dataset bundled with the package for testing.      |

# Examples

``` r
library(siftr)
data(starwars, package = "dplyr")
```

By default, sift searches for exact matches in a column's names, labels, levels, and unique values. As a convenience, you can type bare names in (i.e. `color` instead of `"color"`) for simple queries.

``` r
sift(starwars, color)

#> ℹ Building dictionary for 'starwars'. This only happens when it changes.
#> ✔ Dictionary was built in 0.01 secs.
#> 
#> 4 hair_color
#>         Type: character         Missing: 5 %            All same? No            
#>         Peek: auburn, grey, grey, brown, blond, white, auburn, white, …
#> 5 skin_color
#>         Type: character         Missing: 0 %            All same? No            
#>         Peek: white, blue, grey, red, green-tan, brown, fair, blue, ye…
#> 6 eye_color
#>         Type: character         Missing: 0 %            All same? No            
#>         Peek: blue-gray, yellow, unknown, red, blue, gold, black, haze…
#> 
#> ✔ There were 3 results for query `color`.
```

The `.dist` argument opts-in to approximate searching. It can take an integer (the number of characters that can be flexibly matched) or a double between 0 and 1 (e.g. `0.25` = 25% of the query pattern's length can be flexibly matched).

``` r
sift(starwars, homewolrd, .dist = 0.25)

#> 10 homeworld
#>         Type: character         Missing: 11 %           All same? No            
#>         Peek: Serenno, Trandosha, Aleen Minor, Cerea, Cato Neimoidia, …
#> 
#> ✔ There was 1 result for query `homewolrd`.
```

You can search with regular expressions, but these must be given as Character strings.

``` r
sift(starwars, "gr(a|e)y")

#> 4 hair_color
#>         Type: character         Missing: 5 %            All same? No            
#>         Peek: auburn, grey, grey, brown, blond, white, auburn, white, …
#> 5 skin_color
#>         Type: character         Missing: 0 %            All same? No            
#>         Peek: white, blue, grey, red, green-tan, brown, fair, blue, ye…
#> 6 eye_color
#>         Type: character         Missing: 0 %            All same? No            
#>         Peek: blue-gray, yellow, unknown, red, blue, gold, black, haze…
#> 
#> ✔ There were 3 results for query `gr(a|e)y`.
```

If you give multiple queries, then you will get an orderless look-around search.

``` r
sift(mtcars_lab, gallon, mileage)

#> ℹ Building dictionary for 'mtcars_lab'. This only happens when it changes.
#> ✔ Dictionary was built in 0.01 secs.
#> 
#> 2 mpg
#>     Mileage (miles per gallon)
#>         Type: double      Missing: 0 %      All same? No                        
#>         Peek: 15.2, 21.5, 15, 30.4, 16.4, 14.3, 24.4, 15.5, 19.2, 22.8…
#> 
#> ✔ There was 1 result for query `(?=.*gallon)(?=.*mileage)`.
```

Finally (and most powerfully), you can combine regular expressions and orderless look-around searches.

``` r
sift(starwars, color, "[a-z]{4}_")

#> 4 hair_color
#>         Type: character          Missing: 5 %            All same? No             
#>         Peek: blond, unknown, none, auburn, grey, blonde, brown, auburn,…
#> 
#> 5 skin_color
#>         Type: character          Missing: 0 %            All same? No             
#>         Peek: white, brown mottle, white, blue, fair, green, yellow, blu…
#> 
#> ✔ There were 2 results for query `(?=.*color)(?=.*[a-z]4_)`.
```

## `siftr` works best on labelled data

`sift()` searches through these fields:

1.  A column's name (`colnames(df)`)
2.  Its label (`attr(col, "label")`; placed by many packages including `haven` and `labelled`)
3.  Its value labels (`attr(col, "labels")`; often hold-overs from SPSS or SAS datasets)
4.  Its factor levels (`levels(col)`)
5.  Its unique values (`unique(col)`), sampled at random for large datasets

The more of these fields you can fill out, the more informative and powerful `sift()` will be.

`siftr` pairs well with one of my other packages, [`tsv2label`][1], which can label, rename, and factorise a dataset using a plain text dictionary.




[1]: https://github.com/DesiQuintans/tsv2label
