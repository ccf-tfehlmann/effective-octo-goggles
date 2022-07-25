file_from_earlier.R
================
tfehlmann
2022-07-25

``` r
## how jenny might do this in a first exploration
## purposely leaving a few things to change later!
```

Which libraries does R search for packages?

``` r
.libPaths()
```

    ## [1] "C:/Users/tdhingra/AppData/Local/Programs/R/R-4.2.1/library"

``` r
## let's confirm the second element is, in fact, the default library
.Library
```

    ## [1] "C:/Users/tdhingra/AppData/Local/Programs/R/R-42~1.1/library"

``` r
identical(.Library, .libPaths()[2])
```

    ## [1] FALSE

``` r
## Huh? Maybe this is an symbolic link issue?
library(fs)
identical(path_real(.Library), path_real(.libPaths()[2]))
```

    ## [1] FALSE

Installed packages

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6     ✔ purrr   0.3.4
    ## ✔ tibble  3.1.7     ✔ dplyr   1.0.9
    ## ✔ tidyr   1.2.0     ✔ stringr 1.4.0
    ## ✔ readr   2.1.2     ✔ forcats 0.5.1
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

## how many packages?
nrow(ipt)
```

    ## [1] 212

Exploring the packages

``` r
## count some things! inspiration
##   * tabulate by LibPath, Priority, or both
ipt %>%
  count(LibPath, Priority)
```

    ## # A tibble: 3 × 3
    ##   LibPath                                                    Priority        n
    ##   <chr>                                                      <chr>       <int>
    ## 1 C:/Users/tdhingra/AppData/Local/Programs/R/R-4.2.1/library base           14
    ## 2 C:/Users/tdhingra/AppData/Local/Programs/R/R-4.2.1/library recommended    15
    ## 3 C:/Users/tdhingra/AppData/Local/Programs/R/R-4.2.1/library <NA>          183

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 × 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                 101 0.476 
    ## 2 yes                106 0.5   
    ## 3 <NA>                 5 0.0236

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 × 3
    ##   Built     n   prop
    ##   <chr> <int>  <dbl>
    ## 1 4.2.0    12 0.0566
    ## 2 4.2.1   200 0.943

Reflections

``` r
## reflect on ^^ and make a few notes to yourself; inspiration
##   * does the number of base + recommended packages make sense to you?
##   * how does the result of .libPaths() relate to the result of .Library?
```

Going further

``` r
## if you have time to do more ...

## is every package in .Library either base or recommended?
all_default_pkgs <- list.files(.Library)

all_br_pkgs <- ipt %>%
  filter(Priority %in% c("base", "recommended")) %>%
  pull(Package)

setdiff(all_default_pkgs, all_br_pkgs)
```

    ##   [1] "anytime"       "askpass"       "assertthat"    "backports"    
    ##   [5] "base64enc"     "BH"            "bit"           "bit64"        
    ##   [9] "blob"          "brew"          "brio"          "broom"        
    ##  [13] "bslib"         "cachem"        "callr"         "cellranger"   
    ##  [17] "cli"           "clipr"         "colorspace"    "commonmark"   
    ##  [21] "cpp11"         "crayon"        "credentials"   "crosstalk"    
    ##  [25] "curl"          "data.table"    "DBI"           "dbplyr"       
    ##  [29] "desc"          "devtools"      "diffobj"       "digest"       
    ##  [33] "downlit"       "dplyr"         "dtplyr"        "ellipsis"     
    ##  [37] "evaluate"      "fansi"         "farver"        "fastmap"      
    ##  [41] "fontawesome"   "forcats"       "forecast"      "fracdiff"     
    ##  [45] "fs"            "furrr"         "future"        "future.apply" 
    ##  [49] "gargle"        "generics"      "gert"          "ggplot2"      
    ##  [53] "gh"            "gitcreds"      "globals"       "glue"         
    ##  [57] "googledrive"   "googlesheets4" "gower"         "gtable"       
    ##  [61] "hardhat"       "haven"         "here"          "highr"        
    ##  [65] "hms"           "htmltools"     "htmlwidgets"   "httpuv"       
    ##  [69] "httr"          "ids"           "ini"           "ipred"        
    ##  [73] "isoband"       "jquerylib"     "jsonlite"      "knitr"        
    ##  [77] "labeling"      "later"         "lava"          "lazyeval"     
    ##  [81] "lifecycle"     "listenv"       "lmtest"        "lubridate"    
    ##  [85] "magrittr"      "markdown"      "memoise"       "mime"         
    ##  [89] "miniUI"        "modelr"        "munsell"       "numDeriv"     
    ##  [93] "openssl"       "padr"          "parallelly"    "pillar"       
    ##  [97] "pkgbuild"      "pkgconfig"     "pkgdown"       "pkgload"      
    ## [101] "plotly"        "praise"        "prettyunits"   "processx"     
    ## [105] "prodlim"       "profvis"       "progress"      "progressr"    
    ## [109] "promises"      "ps"            "purrr"         "quadprog"     
    ## [113] "quantmod"      "R6"            "ragg"          "rappdirs"     
    ## [117] "rcmdcheck"     "RColorBrewer"  "Rcpp"          "RcppArmadillo"
    ## [121] "RcppRoll"      "readr"         "readxl"        "recipes"      
    ## [125] "rematch"       "rematch2"      "remotes"       "reprex"       
    ## [129] "rlang"         "rmarkdown"     "roxygen2"      "rprojroot"    
    ## [133] "rsample"       "rstudioapi"    "rversions"     "rvest"        
    ## [137] "sass"          "scales"        "selectr"       "sessioninfo"  
    ## [141] "shiny"         "slider"        "sourcetools"   "SQUAREM"      
    ## [145] "stringi"       "stringr"       "sys"           "systemfonts"  
    ## [149] "testthat"      "textshaping"   "tibble"        "tidyr"        
    ## [153] "tidyselect"    "tidyverse"     "timeDate"      "timetk"       
    ## [157] "tinytex"       "translations"  "tseries"       "tsfeatures"   
    ## [161] "TTR"           "tzdb"          "urca"          "urlchecker"   
    ## [165] "usethis"       "utf8"          "uuid"          "vctrs"        
    ## [169] "viridisLite"   "vroom"         "waldo"         "warp"         
    ## [173] "whisker"       "withr"         "wtfdbg"        "xfun"         
    ## [177] "xml2"          "xopen"         "xtable"        "xts"          
    ## [181] "yaml"          "zip"           "zoo"

``` r
## study package naming style (all lower case, contains '.', etc

## use `fields` argument to installed.packages() to get more info and use it!
ipt2 <- installed.packages(fields = "URL") %>%
  as_tibble()

ipt2 %>%
  mutate(github = grepl("github", URL)) %>%
  count(github) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 × 3
    ##   github     n  prop
    ##   <lgl>  <int> <dbl>
    ## 1 FALSE     59 0.278
    ## 2 TRUE     153 0.722
