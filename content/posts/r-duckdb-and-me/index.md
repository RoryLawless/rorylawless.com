---
title: R, DuckDB and Me
date: 2025-03-30T00:00:00.000Z
keywords:
  - R
  - rstats
  - DuckDB
  - Data
  - Workflow
params:
  author: Rory Lawless
format: hugo-md
---


Over the past year, [DuckDB](https://duckdb.org/docs/stable/clients/r) has gradually become an important part of my data science workflow - at first clumsily, then seamlessly. I don't typically work with large datasets, however, integrating DuckDB has addressed some of my frustrations, especially when dealing with hardware limitations and moderately-sized but inefficiently stored data. With this in mind, here are two major benefits I've found since integrating DuckDB into my workflow.

## Handling larger-than-memory data

As noted, I don't work with very large data often but I still run into annoying issues caused by repeated reloading of data after making mistakes - a habit I call Read-Error-Reread (RERe? Let's make it happen!). Now, this is not an issue for a .csv file containing a few hundred rows and, for larger files or those stored in legacy formats, I could add a "backup" step to my code, like so:

``` r
data <- read.csv("some-data-file.csv")
data_backup <- data

# Do some work on data

# Ahh! I made a mistake, let's try again

data <- data_backup
```

This works fine, but it is a bit of an anti-pattern and ought to, in my opinion, be avoided. Instead of adding this extra step - possibly increasing the memory used in the R session - you can use DuckDB to directly query files stored on disk, without having to load them into memory first.

``` r
library(tidyverse)
library(duckdb)

con <- dbConnect(duckdb::duckdb())

data <- dbGetQuery(
    con,
    "SELECT col_1, col_2, col_4, col_10
    FROM 'some-data-file.csv'
    WHERE col_10 = 'some_value'"
)
```

This may seem more complicated at first, and does require some knowledge of SQL, but it is a very efficient way of working with larger datasets, especially in the early stages when you're still exploring the data and working out what you're going to do with it.

## {duckplyr}

A game-changer for me, which really accelerated my adoption of DuckDB as a backend for processing data, was the [{duckplyr}](https://duckplyr.tidyverse.org) package. Those familiar with [{dbplyr}](https://dbplyr.tidyverse.org) will understand the theory behind this package; it allows queries to be built using the standard set of [{dplyr}](https://dplyr.tidyverse.org) functions, which are then converted to SQL behind the scenes.

``` r
library(tidyverse)
library(duckdb)
library(duckplyr)

con <- dbConnect(duckdb::duckdb())

path_to_some_data_file <- "some-data-file.csv"

data <- tbl_file(con, path_to_some_data_file) |>
    select(col_1, col_2, col_4, col_10) |>
    filter(col_10 == "some_value") |>
    collect()
```

Aside from the `tbl_file()` and `collect()` functions, the rest of the code will be familiar to anyone who has used {dplyr} before. The main advantage of using {duckplyr} over writing SQL and using the [{DBI}](https://dbi.r-dbi.org) package is readability - using common {dplyr} functions makes it accessible to a wider range of users. This is a big benefit for teams where not everyone is comfortable reading or writing SQL.

Additionally, should the original author fall off the face of the earth, the code is still maintainable by others and readily adapted to eliminate the dependency on DuckDB.

## Final thoughts

DuckDB and R are a great combination, allowing me to overcome some of my (self-inflicted?) frustrations in my day-to-day data work. With {duckplyr}, querying data directly from files has smoothed out some of the rough edges in my workflow.
