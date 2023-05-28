# Case Study Bellabeat Product Analysis
Author: Giovani Gutierrez 

Date: 05/23/2023
## Company Information

Bellabeat, a tech-driven wellness company for women, has experienced remarkable growth since its establishment in 2013. With a range of five key products including the Bellabeat app, leaf, time, spring, and Bellabeat membership, the company has established itself as a prominent player in the industry. While currently a successful small company, Bellabeat holds the potential to expand its influence and presence in the global smart device market.

Our team has been entrusted with the task of analyzing smart device data to gain valuable insights into how consumers are utilizing their smart devices. By delving into this data, we aim to uncover patterns, behaviors, and preferences that will serve as a cornerstone for shaping the company's marketing strategy. Through our analysis, we seek to provide actionable recommendations that will help propel Bellabeat towards further success in the market.

## 1. Ask
The objective of this case study is to gain insight into how consumers use non-Bellabeat smart
devices such as Leaf. The study aims to answer the following research questions:

Primary Stakeholders: Urška Sršen and Sando Mur, executive team members.

Secondary stakeholders: Bellabeat marketing analytics team.

a)	What are the most common features or functionalities utilized by consumers in non-Bellabeat smart devices? Can we identify the key activities or metrics that users engage with the most?

b)	How frequently do consumers interact with their non-Bellabeat smart devices? Can we determine the average daily or weekly usage duration and patterns?

c)	Are there any differences in usage patterns between different types or brands of non-Bellabeat smart devices? Can we identify any unique features or functionalities that differentiate one device from another?

d)	Can we identify any user segments or personas based on usage patterns of non-Bellabeat smart devices? Are there specific groups of users who utilize these devices in distinct ways?

## 2. Prepare 
For this case study, we will select a data set (FitBit Fitness Tracker Data (CC0: Public Domain, dataset made avialable through Mobius). The 18 Bellabeat datasets were initially opened in Excel for an initial assessment. They were filtered and sorted to check for blank entries, inconsistent naming, missing data, and potential duplicates.

Reliability: The data is sourced from only 30 FitBit users who provided consent for the submission of their personal tracker data. 

Originality: Data is sourced from a third-party survey by Amazon Mechanical Turk.

Comprehensive: The dataset includes minute-level output for physical activity, heart rate, and sleep monitoring. While it tracks various factors related to user activity and sleep, it is important to note that the sample size is small. Additionally, most of the recorded data is concentrated on specific days of the week, which may impact its overall comprehensiveness for analysis purposes.

Current: The data encompasses the period from March 2016 to May 2016. However, it is not up-to-date, and users' habits may have changed since then.

Cited: The source of the dataset is Mobius via Kaggle. 

 The dataset has limitations:

  a)	The dataset consists of data from only 30 users. While the general rule of the central limit theorem (n≥30) allows for the use of t-tests, a larger sample size is preferred for more robust analysis.

  b)	Upon further investigation using the n_distinct() function to check for unique user IDs, it was discovered that the dataset contains data from 33 users for daily activity, 24 users for sleep, and only 8 users for weight tracking. This indicates the presence of three additional users, and some users did not record their data for daily activity and sleep tracking.

  c)	Most of the data provided didn’t give important information on key characteristics that encompass gender, age, location, and lifestyle. 

  d)	Out of the 8 user data entries for weight, 5 users manually entered their weight, while 3 users recorded it using a connected Wi-Fi device (e.g., Wi-Fi scale).

  e)	Dataset is larger than what excel allows. Cleaning data will be done on RStudio.



## 3. Process

Packages were installed and opened to begin data manipulation, cleaning, analysis, and visualization. Examined the data by checking for missing values, and removing duplicates. 

#### Installing Packages

```install.packages(c("tidyverse", "janitor", "lubridate", "skimr", "plotrix", "geosphere", "scales", "forcats", "readr", "viridis"))```

#### Loading packages

```library(tidyverse)
library(janitor)
library(lubridate)
library(skimr)
library(plotrix)
library(geosphere)
library(scales)
library(forcats)
library(readr)
library(viridis)
```
#### Data importation

```
dailyActivity <- read_csv("dailyActivity_merged.csv")
View(dailyActivity)

dailyCalories <- read_csv("dailyCalories_merged.csv")
View(dailyCalories)

dailyIntensities <- read_csv("dailyIntensities_merged.csv")
View(dailyIntensities)

dailySteps <- read_csv("dailySteps_merged.csv")
View(dailySteps)

heartrateSeconds <- read_csv("heartrate_seconds_merged.csv")
View(heartrateSeconds)

minuteMETs <- read_csv("minuteMETsNarrow_merged.csv")
View(minuteMETs)

sleepDay <- read_csv("sleepDay_merged.csv")
View(sleepDay)

weightLogInfo <- read_csv("weightLogInfo_merged.csv")
View(weightLogInfo)
```
#### Processing data for analysis, checking for errors in data, picking tools, transforming the data, and documenting the cleaning process. Used head(), colnames(), and glimpse() to get a feel for the data. Didn’t need to run it for ever dataset, but I wanted to see how different datasets looked when code was executed. Also looked for things I’d possibly have to change or fix.

```
head(dailyActivity)
colnames(dailyActivity)
glimpse(dailyActivity)

head(dailyCalories)
colnames(dailyCalories)
glimpse(dailyCalories)

head(dailyIntensities)
colnames(dailyIntensities)
glimpse(dailyIntensities)

head(dailySteps)
colnames(dailySteps)
glimpse(dailySteps)

head(heartrateSeconds)
colnames(heartrateSeconds)
glimpse(heartrateSeconds)

head(minuteMETs)
colnames(minuteMETs)
glimpse(minuteMETs)

head(sleepDay)
colnames(sleepDay)
glimpse(sleepDay)

head(weightLogInfo)
colnames(weightLogInfo)
glimpse(weightLogInfo)

colnames(weightLogInfo)[colnames(weightLogInfo) == "WeightPounds"] <- "Weightlbs"
colnames(weightLogInfo)
```

## 4. Analyze 

Used n_distinct(). This function is useful for understanding the level of variation within a dataset. With this we see the number of users in each dataset. Each dataset should have 33 participants. 

```
> n_distinct(dailyActivity$Id)
[1] 33
> n_distinct(dailyCalories$Id)
[1] 33
> 
> n_distinct(dailyIntensities$Id)
[1] 33
> 
> n_distinct(dailySteps$Id)
[1] 33
> 
> n_distinct(heartrateSeconds$Id)
[1] 14
> 
> n_distinct(minuteMETs$Id)
[1] 33
> 
> n_distinct(sleepDay$Id)
[1] 24
> 
> n_distinct(weightLogInfo$Id)
[1] 8
```

heartrateSeconds is missing 16 participants.

sleepDay is missing 9 participants.

weightLogInfo is missing 25 participants. 

#### Converted dailyActivity into a date format, while also adding a column for the days of the week. 

```
dailyActivity <- dailyActivity %>% 
  mutate(weekday1 = weekdays(as.Date(ActivityDate, "%m/%d/%Y")))
glimpse(dailyActivity) 
dailyActivity$weekday1 <- ordered(dailyActivity$weekday1, levels=c("Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"))
activityData <- dailyActivity %>% 
  group_by(weekday1) %>% 
  summarize(count_of = n())
glimpse(activityData)
```

## 5. Share / Visualizations

#### Determining the best way to share my findings. Created effective data visualization. Presented findings and ensured our work was accessible. 

## Data Recorded During the Week

```
ggplot(activityData, aes(x=weekday1, y=count_of)) +
  geom_bar(stat="identity",color="black",fill="#652EB6") +
  labs(title="Data Recorded During the Week", x="Day of the week", y="Count") +
  geom_label(aes(label=count_of),color="black") +
  theme(plot.title = element_text(hjust = 0.5))
```
![Data Recorded During the Week Visualization](https://github.com/ggiovanii8/Case-Study-Bellabeat-Product-Analysis/assets/111608192/dfe892e9-6673-4e82-93d3-fccd1d55ba89)

## Time Asleep vs Time in Bed

```
ggplot(sleepDay, aes(x = TotalTimeInBed, y = TotalMinutesAsleep)) +
  geom_point() +  # Add scatter points
  geom_smooth(method = "lm", se = FALSE) +  # Add correlation line
  labs(x = "Time in Bed", y = "Time Asleep",  title = "Time Asleep vs. Time in Bed")+  # Add axis labels
  theme(plot.title = element_text(hjust = 0.5))

ggplot(activityData, aes(x=weekday1, y=count_of)) +
  geom_bar(stat="identity",color="black",fill="#652EB6") +
  labs(title="Data Recorded During the Week", x="Day of the week", y="Count") +
  geom_label(aes(label=count_of),color="black") +
  theme(plot.title = element_text(hjust = 0.5))
```
![Time Asleep vs  Time in Bed Visualization](https://github.com/ggiovanii8/Case-Study-Bellabeat-Product-Analysis/assets/111608192/feb94b12-0364-4126-b21a-73315975508d)

By analyzing the scatter plot, we can gain insights into sleep patterns. The distribution and clustering of data points provide information about sleep efficiency, including the balance between time spent in bed and actual sleep time. The plot can also highlight variations in sleep duration and identify any noteworthy trends or outliers.

Overall, the "Time Asleep vs. Time in Bed" scatter plot provides a visual representation of the relationship between total time in bed and actual sleep time, helping us understand sleep patterns and identify potential areas for improving sleep quality. The amount of time the participants stayed in bed before falling asleep is approximately 31 minutes.
###### (work on how we got the 40 minutes below)

#### Analyzing our data set. Aggregating our data so its usefull and accessible, organizing and formatting data and identifing trends & relationships. Viewed the dataset sleepDay, and wanted to see how much time was spent in bed before falling asleep. 
 
```
head(sleepDay)
Avg_minutes_asleep <- sleepDay %>% summarize(avg_sleeptime = mean(TotalMinutesAsleep))
Avg_minutes_asleep
AverageTimeInBed <- sleepDay %>%
summarize(AverageTimeInBed = mean(TotalTimeInBed))
AverageTimeInBed
```
#### Subtracting 419(total minutes asleep) from 450(total minutes in bed) gives us the amount of time the participants stayed in bed before falling asleep, which is an additional 31 minutes.

## Calories Burned for Steps Taken

```
head(dailyActivity)

mean_steps <- mean(dailyActivity$TotalSteps)

mean_calories <- mean(dailyActivity$Calories)

ggplot(data=dailyActivity, aes(x=TotalSteps,y=Calories,color=Calories)) +
  geom_point() +
  labs(title="Calories Burned for Steps Taken",x="Steps Taken",y="Calories Burned") +
  geom_smooth(method="lm") +
  geom_hline(mapping = aes(yintercept=mean_calories),color="yellow",lwd=1.0)+
  geom_vline(mapping = aes(xintercept=mean_steps),color="yellow",lwd=1.0) +
  geom_text(mapping = aes(x=8500,y=500,label="Average Steps",srt=-90)) +
  geom_text(mapping = aes(x=25000,y=2500,label="Average Calories")) +
  scale_color_gradient(low="#ffdca7",high="#422d9e") +
  theme(plot.title = element_text(hjust = 0.5))
  ```
![Calories Burned for Steps Taken Visualization](https://github.com/ggiovanii8/Case-Study-Bellabeat-Product-Analysis/assets/111608192/a60162be-ad92-4041-be15-b17584e52c80)

In this visualization, each bar corresponds to a specific range of steps taken, and the height of the bar represents the calories burned for that particular step range. By examining the heights of the bars, we can identify variations in energy expenditure across different step ranges.

The "Calories Burned for Steps Taken" visualization provides insights into the relationship between physical activity, as measured by steps taken, and the resulting calories burned. It allows us to assess the effectiveness of steps taken in terms of calorie burn, providing valuable information for individuals interested in tracking their physical activity and managing their energy expenditure.

## Calories vs Activity Intensity Level

#### Viewing dataset

```
head(dailyIntensities)
head(dailyCalories)
```

#### Loading the viridis package for the color palette.

```
install.packages("viridis")
library(viridis)  
```

#### Setting the layout to 1 row and 2 columns
```
par(mfrow = c(1, 2))
```

#### Scatter plot 1: Calories vs Lightly Active with color gradient
```
plot(dailyIntensities$LightlyActiveMinutes, dailyCalories$Calories, pch = 16, col = viridis(length(dailyCalories$Calories)),
     xlab = "Lightly Active Minutes", ylab = "Calories", main = "")
```

#### Scatter plot 2: Calories vs Very Active with color gradient
```
plot(dailyIntensities$VeryActiveMinutes, dailyCalories$Calories, pch = 17, col = viridis(length(dailyCalories$Calories)),
     xlab = "Very Active Minutes", ylab = "Calories", main = "")
```

#### Resetting the layout to default (1 row and 1 column)

```
par(mfrow = c(1, 1))
```

#### Setting outer margins for the main title

```
par(oma = c(0, 0, 2, 0))
```

#### Adding main title
```
title(main = "Calories vs Activity Intensity Level", outer = TRUE)
```
![Calories vs Activity Intensity Level](https://github.com/ggiovanii8/Case-Study-Bellabeat-Product-Analysis/assets/111608192/0a7b3598-20cf-4ed1-ab6c-ea3782be97af)

In this visualization, each data point represents a specific activity, plotted according to its intensity level on the x-axis and the corresponding calories burned on the y-axis. By examining the distribution and position of the data points, we can gain insights into the relationship between activity intensity and calorie burn.

The "Calories vs Activity Intensity Level" visualization allows us to assess the impact of different activity levels on energy expenditure. It highlights the variations in calorie burn across various activity intensities, providing valuable information for individuals interested in optimizing their physical activity choices and managing their energy balance.

## Total Steps by Day of the Week

#### Analyzing data.

```
head(dailySteps)
head(activityData)
```

#### Loading the necessary libraries.
```
library(scales)
```

#### Converting the date in the dailySteps data frame to the Date format.

```
dailySteps$ActivityDay <- as.Date(dailySteps$ActivityDay, format = "%m/%d/%Y")
```

#### Extracting the day of the week from the ActivityDay column.

```
dailySteps$DayOfWeek <- format(dailySteps$ActivityDay, "%A")
```

#### Reordering the levels of the DayOfWeek factor to match Sunday-Saturday order.

```
dailySteps$DayOfWeek <- factor(dailySteps$DayOfWeek,
                               levels = c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))
```

#### Finding the maximum total steps.

```
max_steps <- max(dailySteps$StepTotal)
```

#### Setting the desired y-axis limit.

```
y_limit <- max_steps * 1.1  # Increase by 10% for some padding
```

#### Defining a color palette.

```
color_palette <- c("#652EB6", "#4D6ED5", "#3693F4", "#26B3E8", "#38D4D9", "#71E3AA", "#A8EE75")
```

#### Creating the bar chart with different colors for each bar.

```
ggplot(dailySteps, aes(x = DayOfWeek, y = StepTotal, fill = DayOfWeek)) +
  geom_bar(stat = "identity") +
  labs(x = "Day of the Week", y = "Step Total", title = "Total Steps by Day of the Week") +
  theme_minimal() +
  scale_y_continuous(limits = c(0, y_limit), labels = comma) +
  scale_fill_manual(values = color_palette) +
  theme(plot.title = element_text(hjust = 0.5))
```
![Total Steps by Day of the Week Visualization](https://github.com/ggiovanii8/Case-Study-Bellabeat-Product-Analysis/assets/111608192/1adc0510-6002-474a-a36c-c69334c5d5c3)

In this visualization, a bar chart is used to display the data. Each bar corresponds to a day of the week, and its height represents the total number of steps recorded for that day. By comparing the heights of the bars, we can easily identify the days with higher or lower step counts.

The "Total Steps by Day of the Week" visualization provides insights into the patterns of physical activity throughout the week. It allows us to identify which days exhibit higher levels of activity and which days may have lower activity levels. This information can be valuable for analyzing trends, identifying potential correlations with other factors, and making informed decisions related to physical fitness and well-being.

## Activity Minutes vs Calories Burned

#### Reshaping the data for plotting.

```
activityMelted <- dailyActivity %>%
  select(Calories, VeryActiveMinutes, FairlyActiveMinutes, LightlyActiveMinutes, SedentaryMinutes) %>%
  pivot_longer(cols = -Calories, names_to = "ActivityLevel", values_to = "ActiveMinutes")
```

#### Plotting the scatter plot with faceting and adjusted transparency.

```
ggplot(activityMelted, aes(x = Calories, y = ActiveMinutes, color = ActivityLevel)) +
  geom_point(size = 3, alpha = 0.5) +
  geom_smooth(method = "lm", se = FALSE, color = "black") +
  labs(x = "Calories Burned", y = "Active Minutes") +
  scale_color_manual(values = c("VeryActiveMinutes" = "blue", 
                                "FairlyActiveMinutes" = "green",
                                "LightlyActiveMinutes" = "orange", 
                                "SedentaryMinutes" = "red")) +
  theme_minimal() +
  facet_wrap(~ ActivityLevel, nrow = 2)
```
![Active Minutes vs  Calories Burned Visualization](https://github.com/ggiovanii8/Case-Study-Bellabeat-Product-Analysis/assets/111608192/cac4d569-2a04-44cc-8417-a72b5c9b61b8)

The "Activity Minutes vs Calories Burned" visualization allows us to understand the relationship between the time spent on physical activity and the resulting calorie burn. It provides insights into the effectiveness of different activity durations in terms of calorie expenditure. By analyzing the scatter plot, we can identify clusters or patterns that indicate higher calorie burn for longer activity durations or vice versa.

This visualization can be helpful in optimizing workout routines, setting fitness goals, and tracking progress towards calorie-burning objectives. It provides valuable information for individuals seeking to make informed decisions regarding their physical activity levels and overall fitness.

## 6. Recommendations / Act

## Bellabeat Mempership

To further drive subscriptions and expand membership growth, Bellabeat can implement the following strategies:

Free Trial User Experience: Introduce a free trial period for the premium service to encourage users to try out the additional features and benefits. This approach allows potential customers to experience the value of the premium service firsthand, increasing the likelihood of converting them into paying subscribers.

Referral and Reward Program: Implement a referral program to incentivize existing members to invite their friends and acquaintances to join Bellabeat. By offering rewards or discounts to both the referrer and the new member, this program can create a positive word-of-mouth effect and generate a steady stream of new subscriptions.

Discounted Smart Device Cost for Members: Provide exclusive pricing or discounts on Bellabeat smart devices for members. This strategy not only adds value to the membership but also acts as an incentive for non-members to join and enjoy the benefits of owning Bellabeat devices.

Partnerships with Health & Fitness Companies: Forge strategic partnerships with established health and fitness companies to enhance Bellabeat's market presence and drive membership growth. Collaborating with reputable brands in the industry can provide opportunities for cross-promotion, joint marketing campaigns, and access to new customer segments.

By implementing these recommendations, Bellabeat can enhance its marketing efforts, attract new subscribers, and foster a strong and engaged community of members.

## Key Points

#### The business task involves analyzing data about select users of FitBit smart devices to draw gainful insights into trends, patterns, and relationships between health parameters. This analysis will help identify potential opportunities for growth and provide high-level marketing recommendations and strategies to Bellabeat's marketing department.

1. By analyzing the data from FitBit smart device users, we can uncover trends such as popular health and fitness activities, preferred features and functionalities, frequency and duration of device usage, sleep patterns, activity intensity levels, and engagement with wellness metrics.

2. Understanding the trends in smart device usage can help identify the preferences and behaviors of Bellabeat's target customers. This knowledge can be used to align Bellabeat's product offerings, app features, and marketing messages with the needs and interests of their customers. For example, if the data reveals that sleep tracking and stress management are popular among smart device users, Bellabeat can focus on enhancing these features in their products to cater to customer demands.

3. The identified trends can provide valuable insights for shaping Bellabeat's marketing strategy. By leveraging the data, Bellabeat can tailor their marketing campaigns to highlight the features and benefits that align with the prevailing trends. They can also identify potential partnerships or collaborations with health and fitness influencers or brands that complement the identified trends. Furthermore, understanding usage patterns can help optimize marketing channels and messages to effectively reach and engage with their target audience, leading to increased brand awareness, customer acquisition, and retention.
