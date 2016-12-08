---
title: 'Peer-graded Assignment: Course Project 1'
author: "Alex Cheung"
date: "6 December 2016"
output: html_document
---
Introduction

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the "quantified self" movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

There are 3 variables included in this dataset are:
```
  1. steps: Number of steps taking in a 5-minute interval (missing values are coded as NA)
  2. date: The date on which the measurement was taken in YYYY-MM-DD format
  3. interval: Identifier for the 5-minute interval in which measurement was taken
```
  
Finally, this dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

---

# Installing the package and getting it running


---
To install the R markdown package and the relevent function for this assigment:


```r
library(ggplot2)
library(knitr)
opts_chunk$set(echo = TRUE, results = 'hold')
```

To load the raw data (.csv) and tidy up the data:

```r
ass <- read.csv("G:/Data/Repro/activity.csv",sep = "," ,header = TRUE)
ass$date <- as.Date(ass$date, format = "%Y-%m-%d")
```

# What is mean total number of steps taken per day?
First to find the sum of steps per day and this question NA value is skipped

```r
steps_per_day = aggregate(steps ~ date, sum, data = ass, na.rm = T)
hist(steps_per_day $steps, xlab = "Total number of steps", main = "No. of steps per day",breaks = 5)
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png)

# Calculate and report the mean and median of the total number of steps taken per day


```r
mean_st <-mean(steps_per_day$steps)
median_st <-median(steps_per_day$steps)
```
The mean of the total number of steps taken per day

```r
mean_st
```

```
## [1] 10766.19
```
The median of the total number of steps taken per day

```r
median_st
```

```
## [1] 10765
```

# What is the average daily activity pattern?

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
avg_daily = aggregate(steps ~ interval, mean, data = ass, na.rm = T)
plot(avg_daily, type = "o",col= "red", pch = 10, main = "Average Number of steps taken within five minutes interval")
lines(avg_daily)
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png)

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
avg_daily[which.max(avg_daily$steps), ]
```

```
##     interval    steps
## 104      835 206.1698
```

# Imputing missing values

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
na= sum(is.na(ass$steps))
na
```

```
## [1] 2304
```

Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
ass1 = merge(ass, avg_daily, by = "interval")

ass1 = ass1[order(ass1$date, ass1$interval), ]

ass$steps[is.na(ass$steps)] = ass1$steps.y[is.na(ass$steps)]
rm(ass1)
```

Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
avg_daily = aggregate(steps ~ date, sum, data = ass)
hist(avg_daily$steps, col = "blue",xlab = "Total number of steps", main = "Number of steps per day",breaks = 10)
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png)

The new mean and median total number of steps taken per day

Mean:

```r
mean(avg_daily$steps)
```

```
## [1] 10766.19
```

Median:

```r
median(avg_daily$steps)
```

```
## [1] 10766.19
```

What is the impact of imputing missing data on the estimates of the total daily number of steps?

```
As you can see, comparing with the calculations done in the first section of this document, we observe that while the mean value remains unchanged, the median value has shifted and virtual matches to the mean.
```

# Are there differences in activity patterns between weekdays and weekends?

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
ass$weekdays = weekdays(ass$date)
weekdays = aggregate(steps ~ interval, mean, data = ass[!(ass$weekdays %in% 
    c("Saturday", "Sunday")), ])
weekends = aggregate(steps ~ interval, mean, data = ass[(ass$weekdays %in% 
    c("Saturday", "Sunday")), ])

par(mfrow = c(2, 1))
plot(weekdays, col = "red",pch = 10, main = "Weekdays")
lines(weekdays)
plot(weekends, col = "green",pch = 10, main = "Weekends")
lines(weekends)
```

![plot of chunk unnamed-chunk-14](figure/unnamed-chunk-14-1.png)

```
In colusion, we can see there are more activity (more steps) during the weekend between the morning and the evening.
```
