# Working with Dates and Times

> How did it get so late so soon? It's night before it's afternoon. December is here before it's June. My goodness how the time has flewn.
> <div align = "right">--- Dr. Seuss</div>

Before we get started, let's load the `lubridate` package. While part of the tidyverse (so you've already got it installed), we have to load it separately, since it's not as commonly used as the other ones:

```r
library(tidyverse)
```

```
## ── Attaching packages ──────────────────────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──
```

```
## ✔ ggplot2 3.1.0     ✔ purrr   0.2.5
## ✔ tibble  1.4.2     ✔ dplyr   0.7.8
## ✔ tidyr   0.8.2     ✔ stringr 1.3.1
## ✔ readr   1.1.1     ✔ forcats 0.3.0
```

```
## ── Conflicts ─────────────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

```r
library(lubridate)
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following object is masked from 'package:base':
## 
##     date
```

Time series data is essential to a huge number of analysis tasks, now that data readers can collect information every hour - or every second. Knowing how a process changes over time - whether it's how warm soil is, how many animals a camera has seen, or how often people have clicked on your link - allows insight into a large variety of questions.

However, dates and times can be surprisingly hard to deal with. For instance, the date the last time I compiled this book was 2019-01-24 - that's YYYY-MM-DD format, which we'll talk about in a minute. At first, this format makes a lot of sense - every 24 hours, we add a 1 to the day; after a certain number of days, we add a 1 to the month; and after 12 months (or 365 days), we add a 1 to the year. It's just a slightly different form of numerical data.

But there's a lot of messiness under those easy rules. For instance, not every year has 365 days, and not every day has 24 hours - thanks to the leap year's lesser-known cousin, the leap-second, but also due to daylight savings time, where we sometimes have 23 or 25 hours in a day. There's really no standardization of how many days a month has. Things are complicated.

Plus, unlike other numerical data, there's no good concept of what 0 means for dates. There's no month 0, there's no day 0, and - confusingly enough - there's no year 0, with year 1 separating the BC and CE eras. And all that confusion comes before the fact that we use [two different calendar systems to refer to years pre- and post-1582](https://en.wikipedia.org/wiki/Gregorian_calendar#Proleptic_Gregorian_calendar).

Luckily enough, you usually won't have a ton of data from before 1582. But it's still worth realizing that dates are just numbers on a kinda weird numberline - or, a perfectly normal numberline, where our labels are a little bit weird.

Due to how tricky numbers can be, this chapter is going to focus entirely on manipulating and wrangling date and time data, without any attempt at analyses. Statistical analyses of time-series data can be extremely complex, and as such are outside the scope of this book; a useful resource on implementing time-series analyses in R may be found [here](https://a-little-book-of-r-for-time-series.readthedocs.io/en/latest/src/timeseries.html).

## Dates in R

R is capable of understanding pure numbers as dates. For instance, if we use the `as_date()` function from `lubridate`, we can convert numbers to dates as such:

```r
as_date(9910)
```

```
## [1] "1997-02-18"
```

To help understand this conversion, it might make sense to call `as_date(0)`:

```r
as_date(0)
```

```
## [1] "1970-01-01"
```

So here we can see what R uses as the 0 for its numberline - instead of being January 1, 1 CE, it chooses January 1, 1970, for... [reasons](https://en.wikipedia.org/wiki/Unix_time). R then adds or subtracts the number of days inside the parentheses to that "0" in order to give us an output date:


```r
as_date(-678)
```

```
## [1] "1968-02-23"
```

Importantly, `as_date()` is doing the fundamentally same thing that `as_character()` or any of the similar functions do - it's converting a numeric object into a date object. We can demonstrate this for ourselves by calling `class()` on an object created by `as_date()`:

```r
DateData <- as_date(9910)
class(9910)
## [1] "numeric"
class(DateData)
## [1] "Date"
```

You can do math with date objects the same way you can with numeric ones - the only difference being that these objects will use the weird timeline we use for dates, incorporating leap years and so on. 

We can also work with date-times, objects which include (you guessed it) both a date and a time. Unlike dates, where adding 1 increments the value by a _day_, adding 1 to a date-time increments by a _second_.

```r
as_datetime(1)
## [1] "1970-01-01 00:00:01 UTC"
as_datetime(9910)
## [1] "1970-01-01 02:45:10 UTC"
```

Date-times are _incredibly_ more complicated than dates, due to the need to manage time zones. Generally speaking, it's best to use the date format unless your data require times be used as well.

## Converting To Dates
Of course, most data won't store dates as integers - we usually store dates in more human readable forms, like 02/18/1997 or 1997-02-18. 

Now, the best format to store dates in is the YYYY-MM-DD format, known as ISO 8601. This format - which is an internationally agreed upon standard - is clear and unambiguous, and lets newcomers to your data know automatically what they're looking at. This format requires you use dashes (-) instead of slashes (/), and that you always use the long form of each number - for instance, January 9, 2018 would be `2018-01-09`, never `18-1-8`. To parse strings formatted like this, use the `lubridate` command `ymd()` - an acronym for how your data is formatted: year-month-day:

```r
ymd("2018-02-18")
```

```
## [1] "2018-02-18"
```

Unfortunately, you won't always be in control of how your data is formatted. Luckily, `lubridate` has a family of functions for understanding dates - just rearrange the letters of the acronym as needed:

```r
dmy("18/02/1997")
```

```
## [1] "1997-02-18"
```

```r
mdy("2/18/97")
```

```
## [1] "1997-02-18"
```

The same family of function can also be used with datetimes, with an underscore and some combination of "h" "m" "s" added:

```r
ymd_hms("2018-02-18 5:20:05 pm")
```

```
## [1] "2018-02-18 17:20:05 UTC"
```

Note that you can either use 24-hour time or specify if a date is AM/PM - R will convert to 24-hour time either way.

We can also build a time out of components stored in different columns, using the `make_date()` or `make_datetime()` commands:

```r
TimeTable <- tibble(year = 2018,
       day = 18,
       hour = 5,
       month = 2,
       seconds = 5,
       minute = 20)

TimeTable %>%
  mutate(date = make_date(year, month, day),
         time = make_datetime(year, month, day, hour, minute, seconds))
```

```
## # A tibble: 1 x 8
##    year   day  hour month seconds minute date       time               
##   <dbl> <dbl> <dbl> <dbl>   <dbl>  <dbl> <date>     <dttm>             
## 1  2018    18     5     2       5     20 2018-02-18 2018-02-18 05:20:05
```

(Note that the inputs in the table are integers, not character vectors like we fed `ymd()` et. al.)

## Extracting From Dates

Say we have the following date:

```r
OurDate <- ymd_hms("2018-02-18 5:20:05 pm")
```

If we only want specific components, we can extract them one by one using a few helper functions:

```r
OurDateExploded <- tibble(year = year(OurDate),
                          month = month(OurDate),
                          day = day(OurDate),
                          hour = hour(OurDate),
                          second = second(OurDate),
                          dayOfWeek = wday(OurDate),
                          dayOfMonth = mday(OurDate),
                          dayOfYear = yday(OurDate))
OurDateExploded
```

```
## # A tibble: 1 x 8
##    year month   day  hour second dayOfWeek dayOfMonth dayOfYear
##   <dbl> <dbl> <int> <int>  <dbl>     <dbl>      <int>     <dbl>
## 1  2018     2    18    17      5         1         18        49
```
(There's no difference between `day()` and `mday()`, as best as I can tell - but you'll see both used in example code online.)

We can also use these commands to change an individual component faster than retyping the whole datetime:

```r
year(OurDate) <-  2020
OurDate
```

```
## [1] "2020-02-18 17:20:05 UTC"
```

We can also use `update()` to change multiple parts of our date at once:

```r
update(OurDate, year = 2021, hour = 19, mday = 20)
```

```
## [1] "2021-02-20 19:20:05 UTC"
```

## Math with Dates
Sometimes, rather than just updating our times by hand, we'll have to do a few more complicated things with them. For instance, if we need to truncate or round our date, we can use one of a family of functions:

* `floor_date()` will round the date down to the unit specified
* `round_date()` will round the date as appropriate to the specified unit
* `ceiling_date()` will round the date up to the unit specified
* `rollback()` will roll the date back to the last day of the previous month

For instance, using each of these with our date looks like this:

```r
floor_date(OurDate, unit = "month")
## [1] "2020-02-01 UTC"
round_date(OurDate, unit = "month")
## [1] "2020-03-01 UTC"
ceiling_date(OurDate, unit = "month")
## [1] "2020-03-01 UTC"
rollback(OurDate)
## [1] "2020-01-31 17:20:05 UTC"
```

More complicated math than that gets a little weird. For instance, say we have a date that's right next to a leap year:

```r
leap <- ymd("2019-03-01")
```

Now, if we wanted to add a year to this date, what would we expect as an output? Probably March 1, 2020, which is what we get if we add `years(1)` to our date:

```r
leap + years(1)
```

```
## [1] "2020-03-01"
```

The problem is, this answer only makes sense if you care more about the unit than what it represents. That is, we just added a year to our date - but that year had 366 days, rather than the 365 we expect a year to represent. 

As such, there are two different families of functions to do math with dates in R, depending on what you want as an output:

* If you want to ignore the irregularities of the time numberline, you're working with **periods** - so adding a year to March 1, 2019, gives us March 1, 2020.
* If you care about the actual passage of time - so adding a year means adding 365 days, no matter what - you're working with **durations**.

Luckily, the functions to work with each are pretty similar - just add a "d" in front of any function that works with periods and you'll be working with durations. For instance:

```r
leap + dyears(1)
```

```
## [1] "2020-02-29"
```

The same problem happens every year with daylight savings time - which is further complicated by the fact that it only happens in certain time zones. Luckily, the same family of functions works in these situations:

```r
lap <- ymd_hms("2018-11-04 00:30:00",tz="US/Eastern") 
lap + minutes(90)
## [1] "2018-11-04 02:00:00 EST"
lap + dminutes(90)
## [1] "2018-11-04 01:00:00 EST"
```

Note, by the way, there's no `dmonths()` function - every year has 12 months, so that one's a lot less likely to trip you up.

Lastly, if you're trying to represent the span in between two times, you can do so using `interval()` - just specify the starting and ending dates in the function:

```r
interval(leap, leap + dyears(1))
```

```
## [1] 2019-03-01 UTC--2020-02-29 UTC
```

## Time Zones
Time zones are complicated as hell, since they're more political boundaries than any sort of real feature of how time passes or any other natural constant. There are over 600 time zones built into R, some with identical names (you'd be shocked how many countries have "Eastern standard time"), and which zone applies to a particular area changes based on time of year and political whims. 

There's been some attempt to standardize how we work with time zones, most importantly by the [Internet Assigned Numbers Authority](https://www.iana.org/time-zones). The IANA specifies names for each zone, _generally_ using `Continent/City` format (`US/Eastern`, `Pacific/Auckland`). You can check your computer's timezone using the `Sys.timezone()` function:

```r
Sys.timezone()
```

```
## [1] "America/New_York"
```

Or see the full list using `OlsonNames()` (warning, it's a long list).

By default, `lubridate` uses UTC, which is the standard for most computing:

```r
OurDate
```

```
## [1] "2020-02-18 17:20:05 UTC"
```

But time zones don't really control anything other than how a date is printed - after all, R is just storing the date as a number, like we showed at the start of this chapter. 

But if you need to change what time zone your data is stored as, there are two methods. First, if you want the same time represented in a different timezone, use `with_tz()`:

```r
with_tz(OurDate, tz = "America/New_York")
## [1] "2020-02-18 12:20:05 EST"
OurDate == with_tz(OurDate, tz = "America/New_York")
## [1] TRUE
```

Secondly, if your data has been mislabeled and you need to change the time zone (and the actual time with it), we can use `force_tz()`:

```r
force_tz(OurDate, tz = "America/New_York")
## [1] "2020-02-18 17:20:05 EST"
OurDate == force_tz(OurDate, tz = "America/New_York")
## [1] FALSE
```

With these functions, you should be all set to start wrangling date and time data with R. 

By the way, the `lubridate` package has a wild amount of edge-case functions built into it - I highly recommend referencing the [cheatsheet](https://rawgit.com/rstudio/cheatsheets/master/lubridate.pdf) available online for functions that we aren't covering here. 
