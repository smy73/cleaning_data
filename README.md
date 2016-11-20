# Getting and Cleaning Data Assignment:
Getting and Cleaning Data, Data Science Specialization, JHU

## Week 4 Assignment Problem Statement

>"The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with your script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.

>One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

>http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

>Here are the data for the project:

>https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

>You should create one R script called run_analysis.R that does the following.

>Merges the training and the test sets to create one data set.
Extracts only the measurements on the mean and standard deviation for each measurement.
Uses descriptive activity names to name the activities in the data set
Appropriately labels the data set with descriptive variable names.
From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject."


## Data Source:

Jorge L. Reyes-Ortiz(1,2), Davide Anguita(1), Alessandro Ghio(1), Luca Oneto(1) and Xavier Parra(2) 1 - Smartlab - Non-Linear Complex Systems Laboratory DITEN - Università degli Studi di Genova, Genoa (I-16145), Italy. 2 - CETpD - Technical Research Centre for Dependency Care and Autonomous Living Universitat Politècnica de Catalunya (BarcelonaTech). Vilanova i la Geltrú (08800), Spain activityrecognition '@' smartlab.ws

Davide Anguita, Alessandro Ghio, Luca Oneto, Xavier Parra and Jorge L. Reyes-Ortiz. A Public Domain Dataset for Human Activity Recognition Using Smartphones. 21th European Symposium on Artificial Neural Networks, Computational Intelligence and Machine Learning, ESANN 2013. Bruges, Belgium 24-26 April 2013.

## Some Information Regarding The Set
The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data. 

The sensor signals (accelerometer and gyroscope) were pre-processed by applying noise filters and then sampled in fixed-width sliding windows of 2.56 sec and 50% overlap (128 readings/window). The sensor acceleration signal, which has gravitational and body motion components, was separated using a Butterworth low-pass filter into body acceleration and gravity. The gravitational force is assumed to have only low frequency components, therefore a filter with 0.3 Hz cutoff frequency was used. From each window, a vector of features was obtained by calculating variables from the time and frequency domain.

## Attribute Information

For each record in the dataset it is provided: 
- Triaxial acceleration from the accelerometer (total acceleration) and the estimated body acceleration. 
- Triaxial Angular velocity from the gyroscope. 
- A 561-feature vector with time and frequency domain variables. 
- Its activity label. 
- An identifier of the subject who carried out the experiment.

## Some Explanations Regarding Code

We applied the same read format to all the files. We used sep= "" in accordance with the given file format. Note that header=FALSE, because we don't need column names now.

We these two file from UCI HAR Dataset and take relevant data.

   ##Reading Features and ActivityLabels vector
   features <- read.csv("features.txt", sep = "", header = FALSE)[2]
   activities <- read.csv("activity_labels.txt", sep = "", header = FALSE)

Again reads from same location and combine "test" and "train" set with the rbind function.

   ##Reading Sets
   testSet <- read.csv("test/X_test.txt", sep = "", header = FALSE)
   trainSet <- read.csv("train/X_train.txt", sep = "", header = FALSE)
   mergedSet <- rbind(testSet,trainSet)        

We follow the same steps as before.

   '''
   ##Reading Movement
   testMoves <- read.csv("test/Y_test.txt", sep = "", header = FALSE)
   trainMoves <- read.csv("train/Y_train.txt", sep = "", header = FALSE)
   mergedMoves <- rbind(testMoves, trainMoves)
'''

   ##Reading PersonID
   testPerson <- read.csv("test/subject_test.txt", sep = "", header = FALSE)
   trainPerson <- read.csv("train/subject_train.txt", sep = "", header = FALSE)
   mergedPerson <- rbind(testPerson, trainPerson)

We assign decriptive column names that is kept in features vector to mergedSet we have formed in previous steps. After that, we select all columns that key values passing through this attributes

   ##Extracting columns which includes measurements
   names(mergedSet) <- features[ ,1]
   mergedSet <- mergedSet[ grepl("std|mean", names(mergedSet), ignore.case = TRUE) ] 

Descriptive values for activity columns.

   #Descriptive ActivityName mapping
   mergedMoves <- merge(mergedMoves, activities, by.x = "V1", by.y = "V1")[2]
   mergedSet <- cbind(mergedPerson, mergedMoves, mergedSet)
   names(mergedSet)[1:2] <- c("PersonID", "Activities")

Tidying set according to personID and activities

   ##Tidying mergedSet
   group_by(mergedSet, PersonID, Activities) %>%
         summarise_each(funs(mean))
