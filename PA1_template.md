---
title: "PA1_template"
output: 
    html_document:
        keep_md: true
---




## 1. Code for reading in the dataset and/or processing the data

```r
activity <- read.csv("activity.csv")

## Transform the date column into Date format
activity$date <- as.Date(activity$date)
```

## 2. Histogram of the total number of steps taken each day

```r
step_pday <- tapply(activity$steps, activity$date, sum)
hist(step_pday)
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

## 3. Mean and median number of steps taken each day
The mean of the total steps per day is `mean(step_pday, na.rm = TRUE)` steps, the median of the total steps per day is `median(step_pday, na.rm = TRUE)` steps.

## 4. Time series plot of the average number of steps taken

```r
step_pinterval <- tapply(activity$steps, activity$interval, mean, na.rm = TRUE)
plot(step_pinterval, type = 'l', xlab = "5-min interval", ylab = "avg steps", 
     main = "avg step taken vs 5-min time interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

## 5. The 5-minute interval that, on average, contains the maximum number of steps

Interval `names(which.max(step_pinterval))` contains the maximum average step, which is `max(step_pinterval)` steps.

## 6. Code to describe and show a strategy for imputing missing data

There are `sum(is.na(activity$steps))` rows that have missing values.


```r
## Impute the missing values, fill the missing values with the mean of the 5-minute interval

nas <- is.na(activity$steps)

## Create a new dataset that is equal to the original dataset but with 
## missing data filled in
activity_full <- activity

for (i in 1:length(nas)) {
    if (nas[i]) {
        interval = as.character(activity_full[i, "interval"])
        activity_full[i, "steps"] = step_pinterval[[interval]]
    }
}
```


## 7. Histogram of the total number of steps taken each day after missing values are imputed

```r
step_pday_full <- tapply(activity_full$steps, activity_full$date, 
                         sum, na.rm = TRUE)
hist(step_pday_full)
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

The mean of the total steps per day is `mean(step_pday_full)` steps, the median of the total steps per day is `median(step_pday_full)` steps. The value of mean didn't change, but the median changed around 1%

## Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends

```r
library(lubridate)
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following objects are masked from 'package:base':
## 
##     date, intersect, setdiff, union
```

```r
## First, create a new column indicate weekdays and weekends
day <- ifelse(wday(activity$date) < 6, yes = "weekdays", no = "weekends")
activity_full <- cbind(activity_full, day)

weekday_activity <- activity_full[activity_full$day == "weekdays", ]
weekend_activity <- activity_full[activity_full$day == "weekends", ]

par(mfrow = c(2,1))
step_pinterval_weekday <- tapply(weekday_activity$steps,
                                 weekday_activity$interval, 
                                 mean, na.rm = TRUE)
plot(step_pinterval_weekday, type = 'l', xlab = "5-min interval", 
     ylab = "avg steps", 
     main = "activity during weekdays")
step_pinterval_weekend <- tapply(weekend_activity$steps,
                                 weekend_activity$interval, 
                                 mean, na.rm = TRUE)
plot(step_pinterval_weekend, type = 'l', xlab = "5-min interval", 
     ylab = "avg steps", 
     main = "activity during weekdays")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png)<!-- -->






