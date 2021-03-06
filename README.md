deltafish now lives [in a new organization](https://github.com/Delta-Stewardship-Council/deltafish). This repository has been archived.


# deltafish

The goal of `deltafish` is to provide easy access to the integrated San Fransisco Delta fish dataset. This dataset is published on the KNB currently, eventually to be published with a DOI at EDI. The dataset contains around 70 million rows, which are not easily queryable with normal R techniques. `deltafish` utilizes the R implementation of [Apache Arrow](https://arrow.apache.org/docs/r/) and the [parquet](https://parquet.apache.org/documentation/latest/) data format, along with `dbplyr` to make the process of working with this large dataset much easier on a standard computer. 

You can use `dplyr` verbs to query the arrow dataset in much the same way as you would a `data.frame`. Instead of computing your function return value every time you execute a `dplyr` function call, however, `arrow` builds a query on the backend which is only run when you `collect()` the data. This means you execute far fewer queries and are able to work with the data much more efficiently.

To begin, install the package as below and run `create_fish_db()`. This will download the data and create the arrow dataset.

## Installation

``` r
devtools::install_github("jeanetteclark/deltafish")
```

## Example

First build the database. This takes a bit to run since the source data are large.

``` r
library(deltaFish)
create_fish_db()
```

Then open and query data.

```r

# open our two data files
surv <- open_survey()
fish <- open_fish()

# filter for sources and taxa of interest
surv_FMWT <- surv %>% 
    filter(Source == "FMWT") %>% 
    select(SampleID, Date)

fish_smelt <- fish %>% 
    filter(Taxa %in% c("Dorosoma petenense", "Morone saxatilis", "Spirinchus thaleichthys"))


# do a join and collect the resulting data frame
# collect executes the sql query and gives you a table
df <- left_join(surv_FMWT, fish_smelt) %>% 
    collect() 

```

