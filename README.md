
<!-- README.md is generated from README.Rmd. Please edit that file -->

# COVID19analytics

<!-- . -->

This package curate (downloads, clean, consolidate, smooth) [data from
Johns Hokpins](https://github.com/CSSEGISandData/COVID-19/) for
analysing international outbreak of COVID-19.

It includes several visualizations of the COVID-19 international
outbreak.

Yanchang Zhao, COVID-19 Data Analysis with Tidyverse and Ggplot2 -
China. RDataMining.com, 2020.

URL:
<http://www.rdatamining.com/docs/Coronavirus-data-analysis-china.pdf>.

  - COVID19DataProcessor generates curated series
  - [visualizations](https://www.r-bloggers.com/coronavirus-data-analysis-with-r-tidyverse-and-ggplot2/)
    by [Yanchang Zhao](https://www.r-bloggers.com/author/yanchang-zhao/)
    are included in ReportGenerator R6 object
  - More visualizations included int ReportGeneratorEnhanced R6 object
  - Visualizations ReportGeneratorDataComparison compares all countries
    counting epidemy day 0 when confirmed cases \> n (i.e. n = 100).

# Consideration

Data is still noisy because there are missing data from some regions in
some days. We are working on in it.

# Package

<!-- badges: start -->

| Release                                                                                                              | Usage                                                                                                    | Development                                                                                                                                                                                            |
| :------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|                                                                                                                      | [![minimal R version](https://img.shields.io/badge/R%3E%3D-3.4.0-blue.svg)](https://cran.r-project.org/) | [![Travis](https://travis-ci.org/rOpenStats/COVID19analytics.svg?branch=master)](https://travis-ci.org/rOpenStats/COVID19analytics)                                                                    |
| [![CRAN](http://www.r-pkg.org/badges/version/COVID19analytics)](https://cran.r-project.org/package=COVID19analytics) |                                                                                                          | [![codecov](https://codecov.io/gh/rOpenStats/COVID19analytics/branch/master/graph/badge.svg)](https://codecov.io/gh/rOpenStats/COVID19analytics)                                                       |
|                                                                                                                      |                                                                                                          | [![Project Status: Active – The project has reached a stable, usable state and is being actively developed.](https://www.repostatus.org/badges/latest/active.svg)](https://www.repostatus.org/#active) |

<!-- badges: end -->

# How to get started (Development version)

Install the R package using the following commands on the R console:

``` r
# install.packages("devtools")
devtools::install_github("rOpenStats/COVID19analytics", build_opts = NULL)
```

# How to use it

``` r
library(COVID19analytics) 
#> Warning: replacing previous import 'ggplot2::Layout' by 'lgr::Layout' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'readr::col_factor' by 'scales::col_factor'
#> when loading 'COVID19analytics'
#> Warning: replacing previous import 'magrittr::equals' by 'testthat::equals' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'magrittr::not' by 'testthat::not' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'magrittr::is_less_than' by
#> 'testthat::is_less_than' when loading 'COVID19analytics'
#> Warning: replacing previous import 'dplyr::matches' by 'testthat::matches' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'testthat::matches' by 'tidyr::matches' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'magrittr::extract' by 'tidyr::extract' when
#> loading 'COVID19analytics'
library(dplyr) 
#> 
#> Attaching package: 'dplyr'
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union
```

``` r
data.processor <- COVID19DataProcessor$new(provider = "JohnsHopkingsUniversity", missing.values = "imputation")

#dummy <- data.processor$preprocess() is setupData + transform is the preprocess made by data provider
dummy <- data.processor$setupData()
#> INFO  [02:20:33.835]  {stage: processor-setup}
#> INFO  [02:20:33.866] Checking required downloaded  {downloaded.max.date: 2020-06-01, daily.update.time: 21:00:00, current.datetime: 2020-06-03 0.., download.flag: TRUE}
#> INFO  [02:20:34.562] Checking required downloaded  {downloaded.max.date: 2020-06-01, daily.update.time: 21:00:00, current.datetime: 2020-06-03 0.., download.flag: TRUE}
#> INFO  [02:20:35.245] Checking required downloaded  {downloaded.max.date: 2020-06-01, daily.update.time: 21:00:00, current.datetime: 2020-06-03 0.., download.flag: TRUE}
#> INFO  [02:20:35.920]  {stage: data loaded}
#> INFO  [02:20:35.922]  {stage: data-setup}
dummy <- data.processor$transform()
#> INFO  [02:20:35.925] Executing transform 
#> INFO  [02:20:35.925] Executing consolidate 
#> INFO  [02:20:37.748]  {stage: consolidated}
#> INFO  [02:20:37.749] Executing standarize 
#> INFO  [02:20:38.216] gathering DataModel 
#> INFO  [02:20:38.218]  {stage: datamodel-setup}
# Curate is the process made by missing values method
dummy <- data.processor$curate()
#> INFO  [02:20:38.221]  {stage: loading-aggregated-data-model}
#> Warning in countrycode(x, origin = "country.name", destination = "continent"): Some values were not matched unambiguously: MS Zaandam
#> INFO  [02:20:39.925]  {stage: calculating-rates}
#> INFO  [02:20:40.108]  {stage: making-data-comparison}
#> INFO  [02:20:45.232]  {stage: applying-missing-values-method}
#> INFO  [02:20:45.234]  {stage: Starting first imputation}
#> INFO  [02:20:45.243]  {stage: calculating-rates}
#> INFO  [02:20:45.455]  {stage: making-data-comparison-2}
#> INFO  [02:20:50.140]  {stage: calculating-top-countries}
#> INFO  [02:20:50.167]  {stage: processed}

current.date <- max(data.processor$getData()$date)

rg <- ReportGeneratorEnhanced$new(data.processor)
rc <- ReportGeneratorDataComparison$new(data.processor = data.processor)

top.countries <- data.processor$top.countries
international.countries <- unique(c(data.processor$top.countries,
                                    "Japan", "Singapore", "Korea, South"))
latam.countries <- sort(c("Mexico",
                     data.processor$countries$getCountries(division = "sub.continent", name = "Caribbean"),
                     data.processor$countries$getCountries(division = "sub.continent", name = "Central America"),
                     data.processor$countries$getCountries(division = "sub.continent", name = "South America")))
```

``` r
# Top 10 daily cases confirmed increment
(data.processor$getData() %>%
  filter(date == current.date) %>%
  select(country, date, rate.inc.daily, confirmed.inc, confirmed, deaths, deaths.inc) %>%
  arrange(desc(confirmed.inc)) %>%
  filter(confirmed >=10))[1:10,]
#> # A tibble: 10 x 7
#> # Groups:   country [10]
#>    country   date       rate.inc.daily confirmed.inc confirmed deaths deaths.inc
#>    <chr>     <date>              <dbl>         <int>     <int>  <int>      <int>
#>  1 Brazil    2020-06-02          0.055         28936    555383  31199       1262
#>  2 US        2020-06-02          0.011         20801   1831821 106180       1031
#>  3 Russia    2020-06-02          0.021          8858    423186   5031        182
#>  4 India     2020-06-02          0.044          8821    207191   5829        221
#>  5 Pakistan  2020-06-02          0.054          3938     76398   1621         78
#>  6 Mexico    2020-06-02          0.042          3891     97326  10637        470
#>  7 Chile     2020-06-02          0.034          3528    108686   1188         75
#>  8 Iran      2020-06-02          0.02           3117    157562   7942         64
#>  9 Banglade… 2020-06-02          0.059          2911     52445    709         37
#> 10 Saudi Ar… 2020-06-02          0.021          1869     89011    549         24
```

``` r
# Top 10 daily deaths increment
(data.processor$getData() %>%
  filter(date == current.date) %>%
  select(country, date, rate.inc.daily, confirmed.inc, confirmed, deaths, deaths.inc) %>%
  arrange(desc(deaths.inc)))[1:10,]
#> # A tibble: 10 x 7
#> # Groups:   country [10]
#>    country   date       rate.inc.daily confirmed.inc confirmed deaths deaths.inc
#>    <chr>     <date>              <dbl>         <int>     <int>  <int>      <int>
#>  1 Brazil    2020-06-02          0.055         28936    555383  31199       1262
#>  2 US        2020-06-02          0.011         20801   1831821 106180       1031
#>  3 Mexico    2020-06-02          0.042          3891     97326  10637        470
#>  4 United K… 2020-06-02          0.006          1656    279392  39452        325
#>  5 India     2020-06-02          0.044          8821    207191   5829        221
#>  6 Russia    2020-06-02          0.021          8858    423186   5031        182
#>  7 France    2020-06-02         -0.005          -898    188450  28943        107
#>  8 Ecuador   2020-06-02          0.034          1316     40414   3438         80
#>  9 Pakistan  2020-06-02          0.054          3938     76398   1621         78
#> 10 Chile     2020-06-02          0.034          3528    108686   1188         75
```

``` r
rg$ggplotTopCountriesStackedBarDailyInc(included.countries = latam.countries,
                                                  map.region = "Latam")
```

<img src="man/figures/README-unnamed-chunk-5-1.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = latam.countries, min.cases = 100)
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
```

<img src="man/figures/README-unnamed-chunk-5-2.png" width="100%" />

``` r

rg$ggplotCountriesLines(included.countries = latam.countries, countries.text = "Latam countries",
                        field = "confirmed.inc", log.scale = TRUE)
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
```

<img src="man/figures/README-unnamed-chunk-5-3.png" width="100%" />

``` r

rc$ggplotComparisonExponentialGrowth(included.countries = latam.countries, field = "deaths", y.label = "Deaths", min.cases = 1)
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
```

<img src="man/figures/README-unnamed-chunk-5-4.png" width="100%" />

``` r

rg$ggplotCrossSection(included.countries = latam.countries,
                       field.x = "confirmed",
                       field.y = "fatality.rate.min",
                       plot.description  = "Cross section Confirmed vs  Death rate min",
                       log.scale.x = TRUE,
                       log.scale.y = FALSE)
```

<img src="man/figures/README-unnamed-chunk-5-5.png" width="100%" />

``` r
rg$ggplotTopCountriesStackedBarDailyInc(top.countries)
#> Warning: Removed 1 rows containing missing values (position_stack).
```

<img src="man/figures/README-unnamed-chunk-6-1.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = international.countries, 
                                               min.cases = 100)
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
```

<img src="man/figures/README-unnamed-chunk-6-2.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = international.countries, field = "deaths", 
                                     y.label = "Deaths", min.cases = 1)
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
```

<img src="man/figures/README-unnamed-chunk-6-3.png" width="100%" />

``` r
rg$ggplotCrossSection(included.countries = international.countries,
                       field.x = "confirmed",
                       field.y = "fatality.rate.min",
                       plot.description  = "Cross section Confirmed vs Death rate min",
                       log.scale.x = TRUE,
                       log.scale.y = FALSE)
```

<img src="man/figures/README-unnamed-chunk-6-4.png" width="100%" />

``` r
rg$ggplotCountriesLines(field = "confirmed.inc", log.scale = TRUE)
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
```

<img src="man/figures/README-unnamed-chunk-7-1.png" width="100%" />

``` r
rg$ggplotCountriesLines(field = "rate.inc.daily", log.scale = TRUE)
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
#> Warning: Transformation introduced infinite values in continuous y-axis
```

<img src="man/figures/README-unnamed-chunk-7-2.png" width="100%" />

``` r
rg$ggplotTopCountriesPie()
```

<img src="man/figures/README-unnamed-chunk-8-1.png" width="100%" />

``` r
rg$ggplotTopCountriesBarPlots()
```

<img src="man/figures/README-unnamed-chunk-8-2.png" width="100%" />

``` r
rg$ggplotCountriesBarGraphs(selected.country = "Argentina")
```

<img src="man/figures/README-unnamed-chunk-9-1.png" width="100%" />
