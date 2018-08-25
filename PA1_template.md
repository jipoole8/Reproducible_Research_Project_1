---
title: "Reproducible Research Project 1"
output: 
  html_document:
    keep_md: true
---
***
### Loading and preprocessing the data
#### 1. Loading the needed libraries

```r
library(ggplot2)
library(Hmisc)
```

```
## Loading required package: lattice
```

```
## Loading required package: survival
```

```
## Loading required package: Formula
```

```
## 
## Attaching package: 'Hmisc'
```

```
## The following objects are masked from 'package:base':
## 
##     format.pval, units
```

#### 2. Process/transform the data

```r
activityData <- read.csv('activity.csv')
```
***
### What is mean total number of steps taken per day?
#### 1. Calculate the total number of steps taken per day

```r
dailySteps <- tapply(activityData$steps, activityData$date, sum)
```

#### 2. Make a histogram of the total number of steps taken each day

```r
hist(dailySteps, xlab = "Steps", main = "Total Steps Per Day")
```

![](PA1_template_files/figure-html/histogram of total steps per day-1.png)<!-- -->

#### 3. Calculate and report the mean and median of the total number of steps taken per day

```r
Mean <- round(mean(dailySteps, na.rm = TRUE), 2)
Median <- (median(dailySteps, na.rm=TRUE))
```
The Mean is 1.076619\times 10^{4}. <br />
The Median is 10765.

***
### What is the average daily activity pattern?
#### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
stepsInterval <- tapply(activityData$steps, activityData$interval, mean, na.rm = TRUE)
plot(as.numeric(names(stepsInterval)), 
     stepsInterval, 
     xlab = "5-minute Interval", ylab = "Average number of Steps", 
     main = "Average Daily Activity Pattern", type = "l")
```

![](PA1_template_files/figure-html/plot of 5-min intervals & avg number steps avgd over all days-1.png)<!-- -->

#### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
maxInterval <- names(sort(stepsInterval, decreasing = TRUE)[1])
maxSteps <- round(max(stepsInterval), 2)
```
The 5-minute interval that contains the maximum number of steps is 835
with 206.17 steps.

***
### Imputing missing values
#### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
missingValues <- sum(is.na(activityData$steps))
```
The total number of missing values in this data set is 2304.

#### 2. Devise a strategy for filling in all of the missing values in the dataset.
Replace missing data in data set with the mean steps across the days.

#### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
actDataImp <- activityData
actDataImp$steps <- impute(activityData$steps, fun=mean)
```

#### 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
dailyStepsImp <- tapply(actDataImp$steps, actDataImp$date, sum)
hist(dailyStepsImp, xlab = "Steps", main = "Total Steps Per Day(Imputed data)")
```

![](PA1_template_files/figure-html/graph of total num of steps per day-1.png)<!-- -->


```r
MeanImp <- round(mean(dailyStepsImp), 2)
MedianImp <- (median(dailyStepsImp))
```
The Mean is 1.076619\times 10^{4}. <br />
The Median is 1.0766189\times 10^{4}.

***
### Are there differences in activity patterns between weekdays and weekends?
#### 1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
actDataImp$date <-  ifelse(as.POSIXlt(actDataImp$date)$wday %in% c(0,6), 'weekend', 'weekday')
```

#### 2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```r
avgActDataImp <- aggregate(steps ~ interval + date, data=actDataImp, mean)
ggplot(avgActDataImp, aes(interval, steps)) + 
    geom_line() + 
    facet_grid(date ~ .) +
    xlab("5-minute interval") + 
    ylab("Avarage Number of Steps")
```

![](PA1_template_files/figure-html/plot of 5min intervals and average num of steps taken-1.png)<!-- -->






