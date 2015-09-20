# Reproducible Research: Peer Assessment 1

```r
    library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
## 
## The following objects are masked from 'package:stats':
## 
##     filter, lag
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
    setwd("C:\\Coursera\\Git\\RepData_PeerAssessment1")
```

## Loading and preprocessing the data

```r
    activity <- read.csv(unz("activity.zip", "activity.csv"), stringsAsFactors = FALSE)
    activity$steps <- as.numeric(activity$steps)
    activity$date <- as.POSIXct(strptime(activity$date, "%Y-%m-%d"),tz = "GMT")
```

## What is mean total number of steps taken per day?

```r
    act_per_day <- group_by(activity, date)
    summarize(act_per_day
              , steps_mean = mean(steps, na.rm = TRUE)
              , steps_median = median(steps, na.rm = TRUE))
```

```
## Source: local data frame [61 x 3]
## 
##          date steps_mean steps_median
## 1  2012-10-01        NaN           NA
## 2  2012-10-02    0.43750            0
## 3  2012-10-03   39.41667            0
## 4  2012-10-04   42.06944            0
## 5  2012-10-05   46.15972            0
## 6  2012-10-06   53.54167            0
## 7  2012-10-07   38.24653            0
## 8  2012-10-08        NaN           NA
## 9  2012-10-09   44.48264            0
## 10 2012-10-10   34.37500            0
## ..        ...        ...          ...
```

## What is the average daily activity pattern?

```r
    act_per_interval <- group_by(activity, interval)
    #act_per_interval <- subset(act_per_interval,interval>700&interval<1000)
    act_per_interval <- mutate(act_per_interval, steps_mean = mean(steps, na.rm = TRUE))
    with(act_per_interval, {
        plot(interval, steps_mean, type = "l", yaxt="n", xaxt="n", ylab="Steps (mean)")
        axis(1, at = seq(100, 2300, by = 100), las=2)
        axis(2, at = seq(10, 200, by = 20), las=2)
        abline(v=835, col="red")
    })
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

## Imputing missing values
    Total number of missing values

```r
    nrow(subset(act_per_interval, is.na(steps)))
```

```
## [1] 2304
```

    Histogram from the original data
    

```r
    hist(activity$steps, main = ""
         , ylim=c(0,15000)
         , xlab = "Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png) 

    Histogram after replacing NA s with the 5-minutes interval mean
    

```r
    act_per_interval$steps[is.na(act_per_interval$steps)] <-
        act_per_interval$steps_mean[is.na(act_per_interval$steps)]

    hist(act_per_interval$steps, main = ""
         , ylim=c(0,15000)
         , xlab = "Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png) 

## Are there differences in activity patterns between weekdays and weekends?

```r
    library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.2.2
```

```r
    library(lubridate)
```

```
## Warning: package 'lubridate' was built under R version 3.2.2
```

```r
    act_per_day <- ungroup(act_per_interval)
    act_per_day <- mutate(act_per_day, day = as.factor( paste(wday(act_per_day$date)
        , weekdays(act_per_day$date, abbreviate = FALSE))))
    
    qplot(steps_mean, interval, data = act_per_day, facets = .~day, xaxt = "n", )
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png) 
