Loading data
============

    if(!file.exists('activity.csv')){
      unzip('activity.zip')
    }
    activityData <- read.csv('activity.csv')

\#Calculate the total number of steps taken per day

    stepsByDay <- tapply(activityData$steps, activityData$date, sum, na.rm=TRUE)

\#Histogram of the total number of steps taken each day

    library(ggplot2)

    ## Registered S3 methods overwritten by 'ggplot2':
    ##   method         from 
    ##   [.quosures     rlang
    ##   c.quosures     rlang
    ##   print.quosures rlang

    qplot(stepsByDay, xlab='Number of steps per day', ylab='Frequency',binwidth=500)

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)

\#Mean and median number of steps taken each day

    stepsByDay_mean <- mean(stepsByDay)
    stepsByDay_median <- median(stepsByDay)

-   Mean: 9354.2295082
-   Median: 10395

\#Time series plot of the average number of steps taken

    averageStepsPerTimeBlock <- aggregate(x=list(meanSteps=activityData$steps), by=list(interval=activityData$interval), FUN=mean, na.rm=TRUE)
    ggplot(data=averageStepsPerTimeBlock, aes(x=interval, y=meanSteps))+geom_line()+xlab('Interval')+ylab('average number of steps')

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-5-1.png)

\#The 5-minute interval that, on average, contains the maximum number of
steps

    mostSteps <- which.max(averageStepsPerTimeBlock$meanSteps)
    timeMostSteps <-  gsub("([0-9]{1,2})([0-9]{2})", "\\1:\\2", averageStepsPerTimeBlock[mostSteps,'interval'])

-   Most Steps at: 8:35

\#Code to describe and show a strategy for imputing missing data

    library(Hmisc)

    ## Loading required package: lattice

    ## Loading required package: survival

    ## Loading required package: Formula

    ## 
    ## Attaching package: 'Hmisc'

    ## The following objects are masked from 'package:base':
    ## 
    ##     format.pval, units

    numMissingValues <- length(which(is.na(activityData$steps)))
    activityDataImputed <- activityData
    activityDataImputed$steps <- impute(activityData$steps, fun=mean)

-   Number of missing values: 2304

\#Histogram of the total number of steps taken each day after missing
values are imputed

    stepsByDay_imputed <- tapply(activityDataImputed$steps, activityDataImputed$date, sum, na.rm=TRUE)
    qplot(stepsByDay_imputed, xlab='Number of steps per day (Imputed)', ylab='Frequency',binwidth=500)

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-8-1.png)

\#Mean and median number of steps taken each day (Imputed)

    stepsByDay_mean_imputed <- mean(stepsByDay_imputed)
    stepsByDay_median_imputed <- median(stepsByDay_imputed)

-   Mean (Imputed): 1.076618910^{4}
-   Median (Imputed): 1.076618910^{4}

\#Panel plot comparing the average number of steps taken per 5-minute
interval across weekdays and weekends

    activityDataImputed$dateType <-  ifelse(as.POSIXlt(activityDataImputed$date)$wday %in% c(0,6), 'weekend', 'weekday')
    averagedActivityDataImputed <- aggregate(steps ~ interval + dateType, data=activityDataImputed, mean)
    ggplot(averagedActivityDataImputed, aes(interval, steps)) + geom_line() + facet_grid(dateType ~ .) +xlab("5-minute interval") + ylab("avarage number of steps")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-10-1.png)
