# README.md file for Course 3 Project - Eduardo Castillo

This README file contains process to get a tydi data using the source ZIP file provided for the Course 3 'Getting and Cleaning Data' Project

The first script you will find below, regards the analysis to get the tydi data frame. The second script at the bottom, regards the 
generation of the Codebook for the data frame.

=======================================================

R Script for Project Course 3: Getting and Cleaning Data

Purpose: This script attempts to perform the following operations:

1. To Merge the training and test sets to create one data set
2. Extract only the measurements on the mean and standard deviation for each measurement
3. Uses descriptive activity names to name the activities in the data set
4. Appropriately labels the data set with descriptive variable names.
5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

# 1. Merging training and set

     # Set working directory and download data

     projectdir <- "./Course3Project"
     dir.create("./Course3Project")
     setwd(projectdir)
     
     # Extract data in working directory
     
     zipfileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
     zipfile <- "UCI HAR Dataset.zip"
     download.file(zipfileURL, zipfile)
     unzip(zipfile)
     
     # Read test data sets and create one data set for test files
     
     Subtest <- read.table("./UCI HAR Dataset/test/subject_test.txt")
     Xtest <- read.table("./UCI HAR Dataset/test/X_test.txt")
     Ytest <- read.table("./UCI HAR Dataset/test/y_test.txt")
     Totaltest <- cbind(Subtest, Ytest, Xtest)
     
     # Read train data sets and create one data set for train files
     
     Subtrain <- read.table("./UCI HAR Dataset/train/subject_train.txt")
     Xtrain <- read.table("./UCI HAR Dataset/train/X_train.txt")
     Ytrain <- read.table("./UCI HAR Dataset/train/y_train.txt")
     Totaltrain <- cbind(Subtrain, Ytrain, Xtrain)
     
     # Combine all into a full data set to merge test and train
     
     mergedSet <- rbind(Totaltest, Totaltrain)
     
          
# 2. Extract only the measurements on the mean and standard deviation for each measurement
     
     # Read features and activity labels
     
     features <- read.table("./UCI HAR Dataset/features.txt")
     activity <- read.table("./UCI HAR Dataset/activity_labels.txt")
     
     # Storing subject and activity columns
     
     allCols <- c("subject", "activity", as.character(features$V2))
     meanStdCols <- grep("subject|activity|[Mm]ean|std", allCols, value = FALSE)
     
     # Subset only mean and std columns
     extractedSet <- mergedSet[ ,meanStdCols]

          
# 3. Uses descriptive activity names to name the activities in the data set
     
     # Matching names to corresponding numbers. Instead of V1 and V2 activity columns titles become Number and Name
     names(activity) <- c("Number","Name")
     
     # Replacing the extracted set in point 2 with new descriptive column names
     extractedSet$V1.1 <- activity$Name[extractedSet$V1.1]
     
      
# 4. Appropriately labels the data set with descriptive variable names.
     
     # Using file 'features_info.txt', replace character patterns to make variables more understandable.
     
     appropiateLabels <- allCols[meanStdCols]
     appropiateLabels <- gsub("acc", "Acceleration", appropiateLabels)
     appropiateLabels <- gsub("gyro", "Gyroscope", appropiateLabels)
     appropiateLabels <- gsub("mad", "Median", appropiateLabels)
     appropiateLabels <- gsub("max", "Largest", appropiateLabels)
     appropiateLabels <- gsub("min", "Lowest", appropiateLabels)
     appropiateLabels <- gsub("irq", "Interquile", appropiateLabels)
     appropiateLabels <- gsub("^anglet", "angleTime", appropiateLabels)
     appropiateLabels <- gsub("^f", "frequency", appropiateLabels)
     appropiateLabels <- gsub("^t", "time", appropiateLabels)    
     appropiateLabels <- gsub("gravity", "Gravity", appropiateLabels)
     appropiateLabels <- gsub("mean", "Mean", appropiateLabels)
     appropiateLabels <- gsub("std", "Standard", appropiateLabels)
     appropiateLabels <- gsub("angle", "Angle", appropiateLabels)
     appropiateLabels <- gsub("[[:punct:]]", "", appropiateLabels)     
     
     # Apply appropiate labels to data set
     names(extractedSet) <- appropiateLabels
     
# 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
     
     # First, group data by activity and subject
     groupedSet <- group_by(extractedSet,activity,subject)
     
     # Second, extract the average of each variable
     summarySet <- summarise_all(groupedSet, funs(mean))
     
     # Write tydi data to output file
     write.table(summarySet, file = "./tidyDataset.txt", row.names = FALSE)
     
====================================================
# Generate Codebook for data frame

This script executed to generate the Codebook for the data frame resulting from the tydi data analysis above.

install.packages("dataMaid")
library("dataMaid")
makeCodebook(summarySet)
