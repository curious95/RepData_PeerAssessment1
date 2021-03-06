Introduction
------------

It is now possible to collect a large amount of data about personal
movement using activity monitoring devices such as a Fitbit, Nike
Fuelband, or Jawbone Up. These type of devices are part of the
“quantified self” movement – a group of enthusiasts who take
measurements about themselves regularly to improve their health, to find
patterns in their behavior, or because they are tech geeks. But these
data remain under-utilized both because the raw data are hard to obtain
and there is a lack of statistical methods and software for processing
and interpreting the data.

This assignment makes use of data from a personal activity monitoring
device. This device collects data at 5 minute intervals through out the
day. The data consists of two months of data from an anonymous
individual collected during the months of October and November, 2012 and
include the number of steps taken in 5 minute intervals each day.

Data
----

The data for this assignment was downloaded from the course web site:

-   Dataset: [Activity monitoring
    data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip)
    \[52K\]

The variables included in this dataset are:

-   **steps**: Number of steps taking in a 5-minute interval (missing
    values are coded as `NA`)

-   **date**: The date on which the measurement was taken in YYYY-MM-DD
    format

-   **interval**: Identifier for the 5-minute interval in which
    measurement was taken

The dataset is stored in a comma-separated-value (CSV) file and there
are a total of 17,568 observations in this dataset.

Setting environment and loading libraries
-----------------------------------------

setting **echo = TRUE** that someone else will be able to read the code
and loading libraries **ggplot2** which will be used for plotting.

    #importing and configuring knitr
    library(knitr)
    opts_chunk$set(echo = TRUE, results = 'hold')

    #loading other required libraries
    library(ggplot2) 
    library(lattice)

Loading and preprocessing the data
----------------------------------

Assuming that the file is already extracted and present in the working
directory we directly load the file.

    #loading the csv file
    activity_data<-read.csv('activity.csv',header = TRUE, sep = ",",stringsAsFactors = FALSE)

    #formatting the date column
    activity_data$date<-as.Date(activity_data$date, format="%Y-%m-%d")

    #converting the interval column to factor type
    activity_data$interval<-as.factor(activity_data$interval)

What is mean total number of steps taken per day?
-------------------------------------------------

#### Calculating the total number of steps taken per day

here the calculation for steps per day is done using tapply. here we
also ignore the the ***missing values***.

    #calculating steps per date
    steps_day <- tapply(activity_data$steps, activity_data$date, FUN = sum, na.rm = TRUE)

#### Histogram of the total number of steps taken each day

    #histogram plot
    hist(steps_day,col = "green",xlab = "Total Steps", ylab = "Frequency",main = "Steps Taken Each Day")

![](R_R_%5BPeer-graded_Assignment__Course_Project_1%5D_files/figure-markdown_strict/hist_plot-1.png)

#### Mean and Median of the total number of steps taken per day

    #mean and median of steps taken per day
    mean_steps   <- mean(steps_day)
    median_steps <- median(steps_day)

The mean and median of total no of steps taken per day are **9354.2295**
and **10395** respectively.

What is the average daily activity pattern?
-------------------------------------------

#### Make a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

    #calculating average number of steps taken in 5-minute interval 
    mean_activity <- aggregate(activity_data$steps, by = list(interval=activity_data$interval), FUN = mean, na.rm = TRUE)

    #renaming columns
    names(mean_activity) <- c("interval", "average")

    #Time series plot
    plot(as.integer(mean_activity$interval),
         mean_activity$average,
         type="l", 
         col="green", 
         xlab="Interval in mins", 
         ylab="Average steps", 
         main="Time-series plot of Average steps in 5 min Interval")

![](R_R_%5BPeer-graded_Assignment__Course_Project_1%5D_files/figure-markdown_strict/t_plot-1.png)

#### Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

    #calculating interval containing max steps
    max_interval<-mean_activity[which.max(mean_activity$average),1]

The 5-minute interval that contains maximum steps is **835**.

Imputing missing values
-----------------------

#### Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with 𝙽𝙰s)

    #calculating missing values
    missing_values <- is.na(activity_data$steps)
    missing_values<-table(missing_values)[2]

Total missing value is **2304**.

#### Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

    #finding NA positions
    pos_na <- which(is.na(activity_data$steps))

    # imputing using mean
    imp_mean <- rep(mean(activity_data$steps, na.rm=TRUE), times=length(pos_na))

#### Create a new dataset that is equal to the original dataset but with the missing data filled in.

    #filling missing values
    activity_data[pos_na, "steps"] <- imp_mean

#### Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

    #calculating steps per date
    steps_day <- tapply(activity_data$steps, activity_data$date, FUN = sum, na.rm = TRUE)

    #histogram plot
    hist(steps_day,col = "green",xlab = "Total Steps", ylab = "Frequency",main = "Steps Taken Each Day")

![](R_R_%5BPeer-graded_Assignment__Course_Project_1%5D_files/figure-markdown_strict/new_hist-1.png)

    #mean and median of steps taken per day
    mean_steps   <- mean(steps_day)
    median_steps <- median(steps_day)

The new mean and median of total no of steps taken per day are
**10766.189** and **10766.189** respectively.

Mean and Median values are increased after the imputation. Mean and
Median also tends to be same after the imputation.

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

#### Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

    #creating a weekday vector
    week_days <- c('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday')

    #specifying week day or weekend based on the weekday vector
    activity_data$daytype <- c('weekend', 'weekday')[(weekdays(activity_data$date) %in% week_days)+1L]

    #sample dataframe after weekday calculation
    head(activity_data)

    ##     steps       date interval daytype
    ## 1 37.3826 2012-10-01        0 weekday
    ## 2 37.3826 2012-10-01        5 weekday
    ## 3 37.3826 2012-10-01       10 weekday
    ## 4 37.3826 2012-10-01       15 weekday
    ## 5 37.3826 2012-10-01       20 weekday
    ## 6 37.3826 2012-10-01       25 weekday

#### Make a panel plot containing a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

    #calculating average steps taken based on weekends and weekdays
    activity_data_mean <- aggregate(activity_data$steps,by=list(activity_data$daytype, activity_data$interval), mean)

    #renamicng the columns
    names(activity_data_mean) <- c("daytype", "interval", "Average")

    #plotting the graph for weekend and week days
    xyplot(Average ~ interval | daytype, activity_data_mean, 
           type="l", 
           xlab="Interval", 
           ylab="Number of steps", 
           layout=c(1,2))

![](R_R_%5BPeer-graded_Assignment__Course_Project_1%5D_files/figure-markdown_strict/week_dayplot-1.png)
