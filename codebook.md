---
title: "Codebook.Rmd"
author: "Marcelladane"
date: "23 July 2017"
output: html_document
---


```r
require(knitr)
require(markdown)

packages <- c("data.table")
sapply(packages, require, character.only = TRUE, quietly = TRUE)
```

```
## data.table 
##       TRUE
```

```r
path <- getwd()
path
```

```
## [1] "C:/Users/User/Documents/Getting clean data/assignment_w4"
```

The run_analysis code will read the **'Human Activity Recognition Using Smartphones Data Set'** avaliable at *https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip* .

Variables description

```r
DataStr <- read.csv("data_structure.csv")
write.table(DataStr, quote=FALSE, sep="\t", row.names=FALSE)
```

```
## Variable.name	Description
## subject	ID the subject who performed the activity for each window sample. Its range is from 1 to 30.
## activity	Activity name
## featDomain	Feature: Time domain signal or frequency domain signal (Time or Freq)
## featInstrument	Feature: Measuring instrument (Accelerometer or Gyroscope)
## featAcceleration	Feature: Acceleration signal (Body or Gravity)
## featVariable	Feature: Variable (Mean or SD)
## featJerk	Feature: Jerk signal
## featMagnitude	Feature: Magnitude of the signals calculated using the Euclidean norm
## featAxis	Feature: 3-axial signals in the X, Y and Z directions (X, Y, or Z)
## featCount	Feature: Count of data points used to compute average
## featAverage	Feature: Average of each variable for each activity and each subject
```

**Dataset structure**

```r
str(Tidy_Data)
```

```
## 'data.frame':	11881 obs. of  11 variables:
##  $ V1 : Factor w/ 31 levels "1","10","11",..: 31 1 1 1 1 1 1 1 1 1 ...
##  $ V2 : Factor w/ 7 levels "activity","LAYING",..: 1 2 2 2 2 2 2 2 2 2 ...
##  $ V3 : Factor w/ 3 levels "featDomain","Freq",..: 1 3 3 3 3 3 3 3 3 3 ...
##  $ V4 : Factor w/ 3 levels "Body","featAcceleration",..: 2 NA NA NA NA NA NA NA NA NA ...
##  $ V5 : Factor w/ 3 levels "Accelerometer",..: 2 3 3 3 3 3 3 3 3 3 ...
##  $ V6 : Factor w/ 2 levels "featJerk","Jerk": 1 NA NA NA NA NA NA NA NA 2 ...
##  $ V7 : Factor w/ 2 levels "featMagnitude",..: 1 NA NA NA NA NA NA 2 2 NA ...
##  $ V8 : Factor w/ 3 levels "featVariable",..: 1 2 2 2 3 3 3 2 3 2 ...
##  $ V9 : Factor w/ 4 levels "featAxis","X",..: 1 2 3 4 2 3 4 NA NA 2 ...
##  $ V10: Factor w/ 46 levels "36","38","39",..: 46 13 13 13 13 13 13 13 13 13 ...
##  $ V11: Factor w/ 11521 levels "-0.000143542544507042",..: 11521 282 1115 10851 5297 6398 5443 5300 5239 1690 ...
```

**List the key variables**

```r
key(Tidy_Data)
```

```
## NULL
```

**Summary of data

```r
summary(Tidy_Data)
```

```
##        V1                        V2                V3      
##  1      : 396   activity          :   1   featDomain:   1  
##  10     : 396   LAYING            :1980   Freq      :4680  
##  11     : 396   SITTING           :1980   Time      :7200  
##  12     : 396   STANDING          :1980                    
##  13     : 396   WALKING           :1980                    
##  14     : 396   WALKING_DOWNSTAIRS:1980                    
##  (Other):9505   WALKING_UPSTAIRS  :1980                    
##                 V4                    V5              V6      
##  Body            :5760   Accelerometer :7200   featJerk:   1  
##  featAcceleration:   1   featInstrument:   1   Jerk    :4680  
##  Gravity         :1440   Gyroscope     :4680   NA's    :7200  
##  NA's            :4680                                        
##                                                               
##                                                               
##                                                               
##              V7                  V8              V9            V10      
##  featMagnitude:   1   featVariable:   1   featAxis:   1   51     : 726  
##  Magnitude    :3240   Mean        :5940   X       :2880   54     : 726  
##  NA's         :8640   SD          :5940   Y       :2880   47     : 660  
##                                           Z       :2880   57     : 594  
##                                           NA's    :3240   59     : 594  
##                                                           48     : 528  
##                                                           (Other):8053  
##                     V11       
##  -0.000971394711666667:    2  
##  -0.0117536873784314  :    2  
##  -0.0128062276916667  :    2  
##  -0.0135771208822581  :    2  
##  -0.0156098198829787  :    2  
##  -0.0197686685018519  :    2  
##  (Other)              :11869
```


**Save to file**

```r
f <- file.path(path, "DatasetHumanActivityRecognitionUsingSmartphones.txt")
write.table(Tidy_Data, f, quote=FALSE, sep="\t", row.names=FALSE)
```

