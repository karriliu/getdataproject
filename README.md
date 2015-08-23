# ##step 1
##loading the Train and Test data into R
activity_labels <- read.table("./UCI HAR Dataset/activity_labels.txt")
features <- read.table("./UCI HAR Dataset/features.txt")
subject_train <- read.table("./UCI HAR Dataset/train/subject_train.txt")
X_train <- read.table("./UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("./UCI HAR Dataset/train/y_train.txt")
subject_test <- read.table("./UCI HAR Dataset/test/subject_test.txt")
X_test <- read.table("./UCI HAR Dataset/test/X_test.txt")
y_test <- read.table("./UCI HAR Dataset/test/y_test.txt")

##merge the train and test dataset together and give column names
xmerge <-cbind(subject_train,y_train,X_train)
ymerge <-cbind(subject_test,y_test,X_test)
data <- rbind(xmerge,ymerge)


#step4 Appropriately labels the data set with descriptive variable names. 
##naming the variables in dataset with features.txt
ft <- as.character(features[,2])
names(data) <- c("subject","label",ft)

#step2
##2.Extracts only the measurements on the mean and standard deviation for each measurement
##store the dataset as data2
data2 <- data[,c("subject","label", colnames(data)[grep("mean()|std()", colnames(data))])]


#step 3
##Uses descriptive activity names to name the activities in the data3
##merge the activity labels with data3 into data4
names(activity_labels) <-c("label","activity")
data3 <- merge(activity_labels,data2,by.x="label",by.y="label", all.x = TRUE)

#step5 creates a second, independent tidy data set with the average of each variable for each activity and each subject.
## tidy the data
library(tidyr)
data4 <-gather(data3[,2:82],variable,value,-subject,-activity)
##calculate the mean by each subject each activity each variable
library(dplyr)
data5 <-summarise(group_by(data4,activity,subject,variable),mean(value))
##rename the columns
names(data5)<- c("activity","subject","variable","average")

##output the data
data5
