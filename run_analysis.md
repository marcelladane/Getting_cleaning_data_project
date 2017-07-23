---
title: "code_book_W4"
author: "Marcelladane"
date: "23 July 2017"
output: html_document
---
## R Markdown

Getting and Cleaning Data Course Projectless 
The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with your script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.

One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Here are the data for the project:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

You should create one R script called run_analysis.R that does the following.

**Merges the training and the test sets to create one data set.**

**Extracts only the measurements on the mean and standard deviation for each measurement.**

**Uses descriptive activity names to name the activities in the data set.**

**Appropriately labels the data set with descriptive variable names.**

**From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.**

You need to start by requiring the use of markdown and knitr, the packages used to create the correct files

```r
require(knitr)
require(markdown)
```

**Set path for your working directory**

```r
packages <- c("data.table", "reshape2")
sapply(packages, require, character.only = TRUE, quietly = TRUE)
```

```
## data.table   reshape2 
##       TRUE       TRUE
```

```r
path <- getwd()
path
```

```
## [1] "C:/Users/User/Documents/Getting clean data/assignment_w4"
```

**DOWNLOAD THE DATA AND UNZIP IT IN THE RIGHT LOCATION**
Download it using the url of the data, set a file to contain your zip file, then you can create a simple if statment to unzip it.
The if statment will basically create a directory path, in case you don't have one and unzip the file there.

```r
url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
f <- "Dataset.zip"
if (!file.exists(path)) {
    dir.create(path)
}
download.file(url, file.path(path, f))
unzip("Dataset.zip") 
```

The result of unzip is a folder called "UCI HAR Dataset" is created, set it as input folder

```r
pathIn <- file.path(path, "UCI HAR Dataset")
list.files(pathIn, recursive = TRUE)
```

```
##  [1] "activity_labels.txt"                         
##  [2] "features.txt"                                
##  [3] "features_info.txt"                           
##  [4] "README.txt"                                  
##  [5] "test/Inertial Signals/body_acc_x_test.txt"   
##  [6] "test/Inertial Signals/body_acc_y_test.txt"   
##  [7] "test/Inertial Signals/body_acc_z_test.txt"   
##  [8] "test/Inertial Signals/body_gyro_x_test.txt"  
##  [9] "test/Inertial Signals/body_gyro_y_test.txt"  
## [10] "test/Inertial Signals/body_gyro_z_test.txt"  
## [11] "test/Inertial Signals/total_acc_x_test.txt"  
## [12] "test/Inertial Signals/total_acc_y_test.txt"  
## [13] "test/Inertial Signals/total_acc_z_test.txt"  
## [14] "test/subject_test.txt"                       
## [15] "test/X_test.txt"                             
## [16] "test/y_test.txt"                             
## [17] "train/Inertial Signals/body_acc_x_train.txt" 
## [18] "train/Inertial Signals/body_acc_y_train.txt" 
## [19] "train/Inertial Signals/body_acc_z_train.txt" 
## [20] "train/Inertial Signals/body_gyro_x_train.txt"
## [21] "train/Inertial Signals/body_gyro_y_train.txt"
## [22] "train/Inertial Signals/body_gyro_z_train.txt"
## [23] "train/Inertial Signals/total_acc_x_train.txt"
## [24] "train/Inertial Signals/total_acc_y_train.txt"
## [25] "train/Inertial Signals/total_acc_z_train.txt"
## [26] "train/subject_train.txt"                     
## [27] "train/X_train.txt"                           
## [28] "train/y_train.txt"
```

**MERGES THE TRAINING AND THE TEST SETS TO CREATE ONE DATA SET**
Read each part of the data: subject, activity, data files 
I create a table to hold each file I read
*used fread to do so*
*said what was the path to the file I wanted to read was located (pathIn) and it will save in my path, that I set before

```r
dataSubTrain <- fread(file.path(pathIn, "train", "subject_train.txt"))
dataSubTest <- fread(file.path(pathIn, "test", "subject_test.txt"))

dataActTrain <- fread(file.path(pathIn, "train", "Y_train.txt"))
dataActTest <- fread(file.path(pathIn, "test", "Y_test.txt"))

dataTrain <- fread(file.path(pathIn, "train", "X_train.txt"))
dataTest <- fread(file.path(pathIn, "test", "X_test.txt"))
```

*Use cbind and rbind to merge the data 3 files of test or training into one*

First set a new table that contains all data from: subject, activity and data
Give labels for the columns for the tables subject and activity, you will need to have one factor in common if you wanna be capable to merge the data afterwards 
Merge all together into one, I used 2 different sets of code, one to merge subject and activity and one to merge the product of it with the data file, called it table_activity

```r
Subject <- rbind(dataSubTrain, dataSubTest)
setnames(Subject, "V1", "subject")
Activity <- rbind(dataActTrain, dataActTest)
setnames(Activity, "V1", "activityNum")
Valeus_training <- rbind(dataTrain, dataTest)

dataSubject <- cbind(Subject, Activity)
table_activity <- cbind(dataSubject, Valeus_training)
```

Set key to facilitate access the data produced

```r
setkey(table_activity, subject, activityNum)
```

Now you have a file with all the data but you don't have the features, what each value actually is. So you need to read another of the original files, called features.

In this file you have all the different labels for activity

**EXTRACTS ONLY THE MEASUREMENTS ON THE MEAN AND STANDARD DEVIATION FOR EACH MEASUREMENT**

Read the files with the features
Name the columns
*Subset just mean and std using grepl*


```r
Features <- fread(file.path(pathIn, "features.txt"))

setnames(Features, names(Features), c("featureNum", "featureName"))

Features <- Features[grepl("mean\\(\\)|std\\(\\)", featureName)]
```

Convert the column numbers to a vector of names matching columns in table_activity.

add this column to the table_activity you created before.


```r
Features$featureCode <- Features[, paste0("V", featureNum)]
head(Features)
```

```
##    featureNum       featureName featureCode
## 1:          1 tBodyAcc-mean()-X          V1
## 2:          2 tBodyAcc-mean()-Y          V2
## 3:          3 tBodyAcc-mean()-Z          V3
## 4:          4  tBodyAcc-std()-X          V4
## 5:          5  tBodyAcc-std()-Y          V5
## 6:          6  tBodyAcc-std()-Z          V6
```

```r
select <- c(key(table_activity), Features$featureCode)
table_activity <- table_activity[, select, with = FALSE]
```

**USES DESCRIPTIVE ACTIVITY NAMES TO NAME THE ACTIVITIES IN THE DATA SETS**

Same as before, you have here a file that give names to the activity, read the right file
Set column names


```r
ActivityNames <- fread(file.path(pathIn, "activity_labels.txt"))

setnames(ActivityNames, names(ActivityNames), c("activityNum", "activityName"))
```

** APPROPRIATELY LABELS THE DATA SET WITH DESCRIPTIVE VARIABLE NAMES**

Set a new key
Reshape the table from a short and wide format to a tall and narrow format.
*Use "melt" to do so* 
*Add the activity names using merge*


```r
table_activity <- merge(table_activity, ActivityNames, by = "activityNum", all.x = TRUE)

setkey(table_activity, subject, activityNum, activityName)

table_activity <- data.table(melt(table_activity, key(table_activity), variable.name = "featureCode"))

table_activity <- merge(table_activity, Features[, list(featureNum, featureCode, featureName)], by = "featureCode", 
    all.x = TRUE)

table_activity$activity <- factor(table_activity$activityName)
table_activity$feature <- factor(table_activity$featureName)
```

The names in feature are made of sets with more than one category, you need to divide them in different columns

Change the abreviated name to something easier to understand

This names can be writen with 1, 2 or 3 categories, therefore you need to write a code that will be capable to handle each case. 

*Use grepthis to do so*


```r
grepthis <- function(regex) {
    grepl(regex, table_activity$feature)
}

n <- 2
y <- matrix(seq(1, n), nrow = n)
x <- matrix(c(grepthis("^t"), grepthis("^f")), ncol = nrow(y))
table_activity$featDomain <- factor(x %*% y, labels = c("Time", "Freq"))
x <- matrix(c(grepthis("Acc"), grepthis("Gyro")), ncol = nrow(y))
table_activity$featInstrument <- factor(x %*% y, labels = c("Accelerometer", "Gyroscope"))
x <- matrix(c(grepthis("BodyAcc"), grepthis("GravityAcc")), ncol = nrow(y))
table_activity$featAcceleration <- factor(x %*% y, labels = c(NA, "Body", "Gravity"))
x <- matrix(c(grepthis("mean()"), grepthis("std()")), ncol = nrow(y))
table_activity$featVariable <- factor(x %*% y, labels = c("Mean", "SD"))

table_activity$featJerk <- factor(grepthis("Jerk"), labels = c(NA, "Jerk"))
table_activity$featMagnitude <- factor(grepthis("Mag"), labels = c(NA, "Magnitude"))

n <- 3
y <- matrix(seq(1, n), nrow = n)
x <- matrix(c(grepthis("-X"), grepthis("-Y"), grepthis("-Z")), ncol = nrow(y))
table_activity$featAxis <- factor(x %*% y, labels = c(NA, "X", "Y", "Z"))
```

Check if all the features worked ok

```r
r1 <- nrow(table_activity[, .N, by = c("feature")])
r2 <- nrow(table_activity[, .N, by = c("featDomain", "featAcceleration", "featInstrument", 
    "featJerk", "featMagnitude", "featVariable", "featAxis")])
r1 == r2
```

```
## [1] TRUE
```

**CREATE A TIDY DATA SET**

```r
setkey(table_activity, subject, activity, featDomain, featAcceleration, featInstrument, featJerk, featMagnitude, featVariable, featAxis)
Tidy_Data <- table_activity[, list(count = .N, average = mean(value)), by = key(table_activity)]
head(Tidy_Data)
```

```
##    subject activity featDomain featAcceleration featInstrument featJerk
## 1:       1   LAYING       Time               NA      Gyroscope       NA
## 2:       1   LAYING       Time               NA      Gyroscope       NA
## 3:       1   LAYING       Time               NA      Gyroscope       NA
## 4:       1   LAYING       Time               NA      Gyroscope       NA
## 5:       1   LAYING       Time               NA      Gyroscope       NA
## 6:       1   LAYING       Time               NA      Gyroscope       NA
##    featMagnitude featVariable featAxis count     average
## 1:            NA         Mean        X    50 -0.01655309
## 2:            NA         Mean        Y    50 -0.06448612
## 3:            NA         Mean        Z    50  0.14868944
## 4:            NA           SD        X    50 -0.87354387
## 5:            NA           SD        Y    50 -0.95109044
## 6:            NA           SD        Z    50 -0.90828466
```
