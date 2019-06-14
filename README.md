Retrieving Historic Weather Data for New Zealand
================
Reean Liao
Feb 2017

-   [Introduction](#introduction)
-   [CliFlo Query with R](#cliflo-query-with-r)
-   [Further Application](#further-application)

### Introduction

To query NZ weather historic data, a subscription to NZ's National Climate Database is needed. The National Climate Database holds climate data from around 6,500 climate stations around New Zealand including some offshore and Pacific Islands. Over 600 stations are currently active and are still receiving valuable climate data.

The database has a web interface called 'CliFlo', which is managed by National Institute of Water and Atmospheric Research (NIWA). Subscription is free and valid for 2 years with 2,000,000 rows of records allowed. Go to <https://cliflo.niwa.co.nz/> to find out how to subscribe.

With the subscription, users can easily download Word, Excel, html or delimited files from the `Database Query Form` on the web interface. This can be done for a certain date range, a selection of 'Datatypes' (weather measures) but only one observation station at a time. This document provides a method to download historic weather data for multiple weather station through R.

<br/>

### CliFlo Query with R

**User account**

The `clifro` package in R is designed to make querying CliFlo much simpler and more efficient. After installing the package, simply load it in the library and pass through your CliFlo subscription user name and password.

``` r
library(clifro)
#set up user account
me <- cf_user("reean_liao", "3WL99YGJ")
```

**Weather Station**

Prior to querying, weather station IDs are required. These can only be retrieved using the web interface on the second section (`2.Location`) of the query form - under `choose station`. When choosing stations, it should be noted that not all stations will provide all weather measures. So to assist with the search, the datatypes of interest should be entered first in the `1.Datatype` section of the web query form.

This demonstration provides the station IDs for 60 weather stations for major cities across NZ. These will be set up in 3 batches since the 'clifro' query can only pass 20 stations at a time.

``` r
#set up station batches, max 20 at a time
my.station1 <- cf_station(41351,22719,4843,37852,1443,3385,1612,26117,2006,
                         1340,38794,5397,1768,3206,41330,4241,40980,3017,3373)
my.station2 <- cf_station(2282,5086,1858,3206,2807,10330,1962,24120,12328,1423,
                         3445,1615,2112,17838,3422,7339,1770,21963,2980)
my.station3 <- cf_station(40751,25119,2973,25531,2283,35703,41429,3719,2810,22254,
                         41228,41230,23976,25354,41077,15752,41077,3243,31830)
```

**Datatypes**

Each data type requires a different set of configurations. These correspond to the option numbers from the menu of the `select datatype(s)` web query form. The menu inludes a 2-levels selection, a set of checkbox and a set of dropdown box. For example, a (2,2,1,1) configuration would mean the datatype to select is the second option from the first page of the menu, second option from the second page, first option in the checkbox set and first option in the dropdown box set.

A few important weather measures are given below.

``` r
#set up datatypes
MaxGust.dt <- cf_datatype(2,2,1,1)
SurfaceWind.dt <- cf_datatype(2,1,4,1)
MinMax.dt <- cf_datatype(4,2,1)
Sunshine.dt <- cf_datatype(5,1,1)
Rain.dt <- cf_datatype(3,1,1) 
```

Alternatively, all weather measures can be bundled up in one collection.

``` r
my.dts <- cf_datatype(select_1 = c(2,2,4,5,3), 
                      select_2 = c(2,1,2,1,1), 
                      check_box = list(1,4,1,1,1), 
                      combo_box = c(1,1,NA,NA,NA))
my.dts
```

    ##                      dt.name              dt.type    dt.options dt.combo
    ## dt1                     Wind             Max Gust       [Daily]      m/s
    ## dt2                     Wind         Surface wind     [9amWind]      m/s
    ## dt3 Temperature and Humidity         Max_min_temp [DailyMaxMin]         
    ## dt4     Sunshine & Radiation             Sunshine       [Daily]         
    ## dt5            Precipitation Rain (fixed periods)       [Daily]

**Querying**

CliFlo data can be retrieved using the code below. Note that each station batch will need to be queried separately; datatypes can be retrieved separately or as a collection.

``` r
#for running 1 datatype at a time
#max gust speed
ls.MaxGust1 <- cf_query(user = me, 
                           datatype = MaxGust.dt, 
                           station = my.station1, 
                           start_date = "2017-01-01 00", 
                           end_date = "2017-02-01 00")
```

    ## connecting to CliFlo...

    ## reading data...

    ## UserName is = reean_liao
    ## Total number of rows output = 279
    ## Number of rows remaining in subscription = 1995640
    ## Copyright NIWA 2019 Subject to NIWA's Terms and Conditions
    ## See: http://clifloecd1.niwa.co.nz/pls/niwp/doc/terms.html
    ## Comments to: cliflo@niwa.co.nz

``` r
ls.MaxGust2 <- cf_query(user = me, 
                           datatype = MaxGust.dt, 
                           station = my.station2, 
                           start_date = "2017-01-01 00", 
                           end_date = "2017-02-01 00")
```

    ## connecting to CliFlo...

    ## reading data...

    ## UserName is = reean_liao
    ## Total number of rows output = 341
    ## Number of rows remaining in subscription = 1995299
    ## Copyright NIWA 2019 Subject to NIWA's Terms and Conditions
    ## See: http://clifloecd1.niwa.co.nz/pls/niwp/doc/terms.html
    ## Comments to: cliflo@niwa.co.nz

``` r
ls.MaxGust3 <- cf_query(user = me, 
                           datatype = MaxGust.dt, 
                           station = my.station3, 
                           start_date = "2017-01-01 00", 
                           end_date = "2017-02-01 00")
```

    ## connecting to CliFlo...

    ## reading data...

    ## UserName is = reean_liao
    ## Total number of rows output = 340
    ## Note: Stations were filtered due to one or more of the following:
    ## No subscription rows remaining
    ## Access restrictions preventing data extraction from a station(s)
    ## Number of rows remaining in subscription = 1994959
    ## Copyright NIWA 2019 Subject to NIWA's Terms and Conditions
    ## See: http://clifloecd1.niwa.co.nz/pls/niwp/doc/terms.html
    ## Comments to: cliflo@niwa.co.nz

or...

``` r
#for running all datatypes as a collection
ls.all1 <- cf_query(user = me, 
                      datatype = my.dts, 
                      station = my.station1,
                      start_date = "2017-01-01 00", 
                      end_date = "2017-02-01 00")
```

    ## connecting to CliFlo...

    ## reading data...

    ## UserName is = reean_liao
    ## Total number of rows output = 1576
    ## Number of rows remaining in subscription = 1993383
    ## Copyright NIWA 2019 Subject to NIWA's Terms and Conditions
    ## See: http://clifloecd1.niwa.co.nz/pls/niwp/doc/terms.html
    ## Comments to: cliflo@niwa.co.nz

``` r
ls.all1
```

    ## List containing clifro data frames:
    ##               data      type              start                end rows
    ## df 1)     Max Gust     Daily (2017-01-01 23:00) (2017-01-31 23:00)  279
    ## df 2) Surface Wind  9am only (2017-01-01  9:00) (2017-01-31  9:00)  248
    ## df 3)      Max_min     Daily (2017-01-01  9:00) (2017-01-31  9:00)  341
    ## df 4)     Sunshine     Daily (2017-01-01 23:00) (2017-01-31 23:00)  310
    ## df 5)         Rain     Daily (2017-01-01  9:00) (2017-01-31  9:00)  398

<br/>

### Further Application

When ready, export the data out using example code below or move on to further analysis.

``` r
#Writing 1 datatype for 1 batch of station at a time
write.csv(ls.MaxGust1, #or ls.all1[1]
          file = "C:\\Users\\Yingy\\Desktop\\MaxGust1.csv", 
          row.names = FALSE)
```

or...

``` r
#Writing all datatypes for 1 bach of stations
for (i in seq_along(ls.all1))
  write.csv(ls.all1[i], 
            file = paste("C:\\Users\\Yingy\\Desktop\\",ls.all1[i]@dt_name,"1.csv",sep=''),
            row.names = FALSE)
```

