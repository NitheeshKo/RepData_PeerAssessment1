---
title: "Reproducible Research: Peer Assessment 1"
output: html_document
---



## Loading and preprocessing the data


```r
library(ggplot2)
library(Hmisc)
library(chron)
```

1. Load the data (i.e. read.csv())

```r
if(!file.exists('activity.csv')){
    unzip('activity.zip')
}
activity<-read.csv('activity.csv',header=TRUE,stringsAsFactors=FALSE)
```

## What is mean total number of steps taken per day?

1.Calculate the total number of steps taken per day


```r
day_act<-aggregate(activity$steps,by=list(activity$date),FUN=sum,na.rm=TRUE)
colnames(day_act)<-c("date","steps")
```

2.Make a histogram of the total number of steps taken each day

```r
hist(day_act$steps,breaks=100)
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png)

3.Calculate and report the mean and median of the total number of steps taken per day

```r
mean(day_act$steps,na.rm=TRUE)
```

```
## [1] 9354.23
```

```r
median(day_act$steps,na.rm=TRUE)
```

```
## [1] 10395
```

## What is the average daily activity pattern?

1.Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
time_act<-aggregate(activity$steps,by=list(activity$interval),FUN=mean,na.rm=TRUE)
colnames(time_act)<-c("interval","steps")
plot(time_act$steps,type="l")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png)

2.Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
time_act[which.max(time_act$steps),1]
```

```
## [1] 835
```

## Imputing missing values

1.Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

2.Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
imputed_activity<-activity
imputed_activity$steps<-impute(activity$steps,fun=mean)
```

3.Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? 

```r
imp_day_act<-aggregate(imputed_activity$steps,by=list(imputed_activity$date),FUN=sum,na.rm=TRUE)
colnames(imp_day_act)<-c("date","steps")
hist(imp_day_act$steps,breaks=100)
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png)

4.What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
mean(imp_day_act$steps,na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
median(imp_day_act$steps,na.rm=TRUE)
```

```
## [1] 10766.19
```

##Are there differences in activity patterns between weekdays and weekends?

1.Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
imp_act<-imputed_activity
imp_act$dayType<-is.weekend(as.Date(imp_act$date))
```

2.Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
imp_time_act<-aggregate(steps~interval+dayType,data=imp_act,FUN=mean,na.rm=TRUE)
day<-c(`FALSE`="weekday",`TRUE`="weekend")
ggplot(imp_time_act,aes(interval,steps))+
geom_line() + 
    facet_grid(dayType~ ., labeller = as_labeller(day)) +
    xlab("5-minute interval") + 
    ylab("avarage number of steps")
```

![plot of chunk unnamed-chunk-14](figure/unnamed-chunk-14-1.png)
