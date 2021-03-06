Statistical assignment 1
================
Daniel Orchard 680040213
30 Jan 2020

## Open data (10 points)

In this assignment you will work with the individual level data from
wave 8 of the Understanding Society survey. First, you need to open the
data set. Please complete the code below.

``` r
library(tidyverse)
```

    ## Warning: package 'tidyverse' was built under R version 3.5.3

    ## -- Attaching packages --------------------------------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.2.1     v purrr   0.3.3
    ## v tibble  2.1.3     v dplyr   0.8.3
    ## v tidyr   1.0.0     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.4.0

    ## Warning: package 'ggplot2' was built under R version 3.5.3

    ## Warning: package 'tibble' was built under R version 3.5.3

    ## Warning: package 'tidyr' was built under R version 3.5.3

    ## Warning: package 'readr' was built under R version 3.5.3

    ## Warning: package 'purrr' was built under R version 3.5.3

    ## Warning: package 'dplyr' was built under R version 3.5.3

    ## Warning: package 'stringr' was built under R version 3.5.3

    ## Warning: package 'forcats' was built under R version 3.5.3

    ## -- Conflicts ------------------------------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(dplyr)
Data <- read_tsv("h_indresp.tab")
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_double()
    ## )

    ## See spec(...) for full column specifications.

Now you have got your data frame stored as Data.

## Select variables (10 points)

The data for Wave 8 of the Understanding Society were collected in
2016-18. Among other things, people were asked the following question:
“Should the United Kingdom remain a member of the European Union or
leave the European Union?” In this assignment, we will explore how
answers to this question depend on sex and age.

First, you need to select the variables for the analysis. You want to
keep the following variables: cross-wave individual identifier (*pidp*),
support for the UK remaining or leaving the EU (*h\_eumem*), sex
(*h\_sex\_dv*), age (*h\_age\_dv*), and sample origin (*h\_memorig*).

Complete the code below to select those variables from the data frame
and save the result.

``` r
Data <- Data %>%
        select(pidp, h_eumem, h_sex_dv, h_age_dv, h_memorig)
```

## Filter observations (10 points)

To make nationally representative estimates from the Understanding
Society data we would need to use weight coefficients. There are many
different types of weight coefficients that can be used depending on the
question and the level of analysis (see the User Guide, pp. 65-71). We
will not do this in this assignment. However, what we want to do is to
keep data from the original Understanding Society sample only (ukhls gb
2009-10), dropping data for Northern Ireland, the BHPS cohort members
and ethnic minority boost samples. This will make data closer to be
representative for Great Britain. You need to choose the observations
where *h\_memorig* has the value of 1.

``` r
Data <- Data %>%
        filter(h_memorig == 1)

table(Data$h_memorig)
```

    ## 
    ##     1 
    ## 22957

``` r
## All are from 1 
```

## Recode data (20 points)

Let us tabulate the variables for EU support, sex, and age.

``` r
table(Data$h_eumem)
```

    ## 
    ##    -9    -8    -7    -2    -1     1     2 
    ##    33   482   879   354   753 11118  9338

``` r
table(Data$h_sex_dv)
```

    ## 
    ##     0     1     2 
    ##     1 10470 12486

``` r
table(Data$h_age_dv)
```

    ## 
    ##  16  17  18  19  20  21  22  23  24  25  26  27  28  29  30  31  32  33  34  35 
    ## 284 309 290 291 278 295 268 326 287 257 243 234 229 249 274 278 278 293 314 332 
    ##  36  37  38  39  40  41  42  43  44  45  46  47  48  49  50  51  52  53  54  55 
    ## 351 332 321 336 320 327 368 404 372 386 435 465 425 447 406 420 427 414 432 422 
    ##  56  57  58  59  60  61  62  63  64  65  66  67  68  69  70  71  72  73  74  75 
    ## 408 413 416 434 369 398 358 399 354 412 345 358 412 434 431 334 326 293 275 251 
    ##  76  77  78  79  80  81  82  83  84  85  86  87  88  89  90  91  92  93  94  95 
    ## 219 231 211 205 181 162 138 117 117 108  89  78  77  48  41  27  15  18  15   7 
    ##  96  97  98  99 101 102 
    ##   6   2   3   1   1   1

You will see that all these variables are numeric. You can learn what
the numeric codes mean by checking the codebook here:
<https://www.understandingsociety.ac.uk/documentation/mainstage/dataset-documentation/datafile/indresp/wave/8>
.

We want to do the following:

1)  Recode the variable for EU support as binary (1 for Remain, 0 for
    Leave), coding all types of missing values (including refusals and
    “don’t know”) as NA.
2)  Recode sex into a character vector with the values “male” or
    “female”.
3)  Recode age into a variable with the following categories: 16 to 25,
    26 to 40, 41 to 55, 56 to 70, over 70.

In all cases, we want to create new variables.

``` r
Data <- mutate(Data, 
               
         EU = case_when(
           h_eumem == 1 ~ 1,
           h_eumem == 2 ~ 0,
           h_eumem < 0 ~ NA_real_),
         
         sex = case_when(
           h_sex_dv == 1 ~ "male",
           h_sex_dv == 2 ~ "female"),
        
         agegr = case_when(
          h_age_dv %in% 16:25 ~ "16-25",
          h_age_dv %in% 26:40 ~ "26-40",
          h_age_dv %in% 41:55 ~ "41-55",
          h_age_dv %in% 56:70 ~ "56-70",
          h_age_dv > 70 ~ "70+"))
            
table(Data$EU)
```

    ## 
    ##     0     1 
    ##  9338 11118

``` r
table(Data$sex)
```

    ## 
    ## female   male 
    ##  12486  10470

``` r
table(Data$agegr)
```

    ## 
    ## 16-25 26-40 41-55 56-70   70+ 
    ##  2885  4384  6150  5941  3597

## Summarise data (20 points)

Let us **dplyr** to calculate how many people in the sample supported
Remain and Leave, both as absolute numbers and percentages.

``` r
     Data %>%
  count(EU) %>%
  mutate(perc = n / sum(n) * 100)
```

    ## # A tibble: 3 x 3
    ##      EU     n  perc
    ##   <dbl> <int> <dbl>
    ## 1     0  9338  40.7
    ## 2     1 11118  48.4
    ## 3    NA  2501  10.9

\#\#Write a couple of sentences with the interpretation of this result.
How this compares with the result of the 2016 referendum? Why?

The results from these tables are very different to the actual results
of the 2016 referendum. In 2016 51.89% voted Leave and 48.11% voted
Remain resulting in a Leave victory. These sample results show a Remain
victory with 48% voting Remain and 41% voting Leave.

This may be due to the supersampling that takes place in this survey, as
possible ethnic minorities, who are overrepresented in this data, are
more likely to vote Remain. It could also be because those who voted
Leave may be included in the NA row for missing data.

## Summarise data by sex and age (30 points)

Now let us look at the support for Leave and Remain by sex and age. Use
your newly created variables.

``` r
Data %>%  group_by(sex, agegr) %>%
  summarise(
    Remain_Support = mean(EU, na.rm = TRUE) * 100,
    Leave_Support = 100 - Remain_Support

  )
```

    ## # A tibble: 11 x 4
    ## # Groups:   sex [3]
    ##    sex    agegr Remain_Support Leave_Support
    ##    <chr>  <chr>          <dbl>         <dbl>
    ##  1 female 16-25           72.6          27.4
    ##  2 female 26-40           64.0          36.0
    ##  3 female 41-55           56.9          43.1
    ##  4 female 56-70           50.5          49.5
    ##  5 female 70+             44.2          55.8
    ##  6 male   16-25           66.1          33.9
    ##  7 male   26-40           58.8          41.2
    ##  8 male   41-55           51.7          48.3
    ##  9 male   56-70           47.6          52.4
    ## 10 male   70+             37.6          62.4
    ## 11 <NA>   16-25          100             0

70 7 56 3 41 5 26 6 16 6

Write a couple of sentences interpreting your results.

This data appears to show that older people tend to support leave. For
both sexes, the age category with the highest remain support is 16-25,
and the category with the leave is 70+. It also seems that women support
remaining in the EU slightly more than men as for each age category they
have higher Remain support that their male counterparts. This ranges
from a 3 point difference in the 56-70 category to a 7 point difference
in the 70+ section.
