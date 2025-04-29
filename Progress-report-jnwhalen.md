Progress-report-jnwhalen
================
Jenna Whalen

## GitHub Documents

``` r
library(Lahman)
head(HallOfFame, 3)
```

    ##    playerID yearID votedBy ballots needed votes inducted category needed_note
    ## 1 aaronha01   1982   BBWAA     415    312   406        Y   Player        <NA>
    ## 2 abbotji01   2005   BBWAA     516    387    13        N   Player        <NA>
    ## 3 abreubo01   2020   BBWAA     397    298    22        N   Player        <NA>

## Reading

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(readr)
library(ggplot2)
```

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
url <- "https://www.baseball-reference.com/awards/hof_2025.shtml"
html <- read_html(url)
tables <- html_table(html)
head(tables[[1]], 3)
```

    ## # A tibble: 3 × 39
    ##   ``    ``           ``    ``    ``    ``    ``    ``    ``    ``    ``    ``   
    ##   <chr> <chr>        <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr>
    ## 1 Rk    Name         YoB   Votes %vote HOFm  HOFs  Yrs   WAR   WAR7  JAWS  Jpos 
    ## 2 1     Ichiro Suzu… 1st   393   99.7% 235   44    19    60.0  43.7  51.8  56.0 
    ## 3 2     CC Sabathia  1st   342   86.8% 128   48    19    62.3  39.4  50.8  61.3 
    ## # ℹ 27 more variables: `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Pitching Stats` <chr>,
    ## #   `Pitching Stats` <chr>, `Pitching Stats` <chr>, `Pitching Stats` <chr>,
    ## #   `Pitching Stats` <chr>, `Pitching Stats` <chr>, `Pitching Stats` <chr>, …

``` r
data <- tables[[1]]
actual_col_names <- data[1, ]
colnames(data) <- actual_col_names
data <- data[-1, ]
head(data, 3)
```

    ## # A tibble: 3 × 39
    ##   Rk    Name       YoB   Votes `%vote` HOFm  HOFs  Yrs   WAR   WAR7  JAWS  Jpos 
    ##   <chr> <chr>      <chr> <chr> <chr>   <chr> <chr> <chr> <chr> <chr> <chr> <chr>
    ## 1 1     Ichiro Su… 1st   393   99.7%   235   44    19    60.0  43.7  51.8  56.0 
    ## 2 2     CC Sabath… 1st   342   86.8%   128   48    19    62.3  39.4  50.8  61.3 
    ## 3 3     Billy Wag… 10th  325   82.5%   107   24    16    27.7  19.8  23.7  31.6 
    ## # ℹ 27 more variables: G <chr>, AB <chr>, R <chr>, H <chr>, HR <chr>,
    ## #   RBI <chr>, SB <chr>, BB <chr>, BA <chr>, OBP <chr>, SLG <chr>, OPS <chr>,
    ## #   `OPS+` <chr>, W <chr>, L <chr>, ERA <chr>, `ERA+` <chr>, WHIP <chr>,
    ## #   G <chr>, GS <chr>, SV <chr>, IP <chr>, H <chr>, HR <chr>, BB <chr>,
    ## #   SO <chr>, `Pos Summary` <chr>

## Cleaning

``` r
tables <- html %>% html_elements("table") %>% html_table(fill = TRUE)

valid_names <- names(data) != "" & !is.na(names(df))

df <- tables[[1]]

data$Votes <- readr::parse_number(data$Votes)
data$`%vote` <- readr::parse_number(data$`%vote`)
data$Name <- gsub("X-", "", data$Name)
data$Name <- gsub("\\.", "", data$Name)    # Remove periods
data$Name <- gsub("  ", " ", data$Name)    # Fix double spaces
head(data, 3)
```

    ## # A tibble: 3 × 39
    ##   Rk    Name       YoB   Votes `%vote` HOFm  HOFs  Yrs   WAR   WAR7  JAWS  Jpos 
    ##   <chr> <chr>      <chr> <dbl>   <dbl> <chr> <chr> <chr> <chr> <chr> <chr> <chr>
    ## 1 1     Ichiro Su… 1st     393    99.7 235   44    19    60.0  43.7  51.8  56.0 
    ## 2 2     CC Sabath… 1st     342    86.8 128   48    19    62.3  39.4  50.8  61.3 
    ## 3 3     Billy Wag… 10th    325    82.5 107   24    16    27.7  19.8  23.7  31.6 
    ## # ℹ 27 more variables: G <chr>, AB <chr>, R <chr>, H <chr>, HR <chr>,
    ## #   RBI <chr>, SB <chr>, BB <chr>, BA <chr>, OBP <chr>, SLG <chr>, OPS <chr>,
    ## #   `OPS+` <chr>, W <chr>, L <chr>, ERA <chr>, `ERA+` <chr>, WHIP <chr>,
    ## #   G <chr>, GS <chr>, SV <chr>, IP <chr>, H <chr>, HR <chr>, BB <chr>,
    ## #   SO <chr>, `Pos Summary` <chr>

``` r
HallOfFame_2025 <- data.frame(
  yearID = 2025,
  votedBy = "BBWAA",
  ballots = as.numeric(data$Votes),
  needed = NA,
  votes = as.numeric(data$Votes),
  inducted = ifelse(as.numeric(data$`%vote`) >= 75, "Y", "N"),
  category = "Player"
)
```
