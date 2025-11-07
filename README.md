---
title: "Cyclistic Case Study"
output:
  html_document:
    df_print: paged
  html_notebook: default
  pdf_document: default
  word_document: default
---

# Preparation

## Initially, I did my data cleaning in Excel.

I combined the data from the past 12 months that was found in [divvy-tripdata](https://divvy-tripdata.s3.amazonaws.com/index.html) into a single sheet using the Power Query Editor. I then removed unnecessary data. Since the question I was assigned was "How do annual members and casual riders use Cyclistic bikes differently?", I deduced that the starting and ending locations, the starting and ending stations as well as the starting and ending longitudes and latitudes were not needed. Additionally, the ride id's were not needed either. By removing these unnecessary sources of data, I can then focus on key elements to make my analysis.

After cleaning the data, I then focus on how to answer how annual members usage differs from that of casual members. To see a clear picture of this, I decide to expand on the data by adding columns based off Month, Day, Hour and Ride Durations. To do this, I used the Power Query Editor on Excel. In order to calculate trip durations, I subtracted the time a ride ended by the time the ride started. From there, I saw that most ride durations were less than an hour and decided to display the data by minutes. Then, I separated the Ride Time into categories: Under 10 minutes, 10-30, 30-60, and Over 60 minutes. This allowed me to see how the bikes were being used and I could then separate the data by use either by members or casuals. 

## Loading packages and libraries.

## I loaded the following packages.
```{r}
#install.packages("tidyverse")
#install.packages("ggplot2")
#install.packages("dplyr")
#install.packages("readr")
```

## I then loaded the libraries.
```{r}
library(ggplot2)
library(dplyr)
library(readr)
library(readxl)  # For read_excel()
library(tidyverse)  # Optional but helpful
```

## Next, I loaded my cleaned data.
```{r}
Cyclistic <- read_excel("C:/Users/smaga/OneDrive/Desktop/Stuff/Data Analytics/Data Analytics Bootcamp/Case Study 1/Cyclistic.xlsx")
```

### I realized that some of my column names included spaces so I fixed them by renaming the columns.
```{r}
colnames(Cyclistic)[4] <- "Day_Name"
colnames(Cyclistic)[6] <- "Ride_Time"
```

# Visualization

## Deciding useful visualization to make analysis

Going back to my initial assignment to answer "How do annual members and casual riders use Cyclistic bikes differently?", I decided that seeing the breakdowns of Monthly, Daily, hourly and duration usage would be best to see any trends.

## Making visualizations


Now to see my visualizations, I decided that the best way to see my data is by using bar charts. Additionally, I would like to compare annual members to casual riders, so I organize my bar charts to have them show side by side using the the position="dodge" function. In order to keep a consistent color scheme, I assign colors to members and casuals.

```{r}
member_colors <- c("casual" = "#E69F00", "member" = "#0072B2")
```

Using the following code, I am able to see my data by:

### Monthly Usage
```{r}
ggplot(data = Cyclistic) +
     geom_col(mapping= aes(x = Month_Name, y= Count, fill=member_casual), position="dodge")
```

I noticed that the x axis was showing the months in ascending order which I did not want. I wanted them to show in chronological order of the months progression in the year so I reordered their display by using the levels function.
```{r}
Cyclistic$Month_Name<-factor(Cyclistic$Month_Name, levels = c("Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"))
```
When I saw that my data displayed how I wanted, I enhanced the visual by adding titles and labels to make the data presented more clear.
```{r}
ggplot(data = Cyclistic) +
  geom_col(mapping = aes(x = Month_Name, y = Count, fill = member_casual), 
           position = "dodge") +
  scale_fill_manual(values = member_colors,
                    labels = c("Casual Riders", "Annual Members")) +
  labs(title = "Monthly Bike Usage: Members vs Casual Riders",
       subtitle = "Usage patterns are similar across both groups",
       x = "Month",
       y = "Number of Riders",
       fill = "Rider Type") +
  theme(axis.text.x = element_text(angle = 45, hjust = 1),
        legend.position = "top")
```

### Daily Usage
```{r}
ggplot(data = Cyclistic) +
     geom_col(mapping= aes(x = Day_Name, y= Count, fill=member_casual), position="dodge")
```

I found the same issue of the x axis ordering itself in ascending order so I also used the levels function to reorder them to show the data from sunday to saturday.
```{r}
Cyclistic$Day_Name<-factor(Cyclistic$Day_Name, levels = c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))
```
When I saw that my data displayed how I wanted, I enhanced the visual by adding titles and labels to make the data presented more clear.
```{r}
plot_daily <- ggplot(data = Cyclistic) +
  geom_col(mapping = aes(x = Day_Name, y = Count, fill = member_casual), 
           position = "dodge") +
  scale_fill_manual(values = member_colors,
                    labels = c("Casual Riders", "Annual Members")) +
  labs(title = "Daily Bike Usage: Members vs Casual Riders",
       subtitle = "Members commute on weekdays; casuals ride more on weekends",
       x = "Day of Week",
       y = "Number of Riders",
       fill = "Rider Type") +
  theme(axis.text.x = element_text(angle = 45, hjust = 1),
        legend.position = "top")
```

### Hourly Usage
```{r}
ggplot(data = Cyclistic) +
     geom_col(mapping= aes(x = Hour, y= Count, fill=member_casual), position="dodge")
```

The intial visualization grouped my hours by intervals of 5 which I did not want because I wanted to see each hours usage clearly separated. To do that, I used the factor function to breakdown each hour.
```{r}
ggplot(data = Cyclistic) +
     geom_col(mapping= aes(x = factor(Hour), y= Count, fill=member_casual), position="dodge")
```
When I saw that my data displayed how I wanted, I enhanced the visual by adding titles and labels to make the data presented more clear.
```{r}
ggplot(data = Cyclistic) +
  geom_col(mapping = aes(x = factor(Hour), y = Count, fill = member_casual), 
           position = "dodge") +
  scale_fill_manual(values = member_colors,
                    labels = c("Casual Riders", "Annual Members")) +
  labs(title = "Hourly Bike Usage: Members vs Casual Riders",
       subtitle = "Members show clear commute peaks (8am & 5pm); casuals peak midday",
       x = "Hour of Day",
       y = "Number of Riders",
       fill = "Rider Type") +
  theme(legend.position = "top")
```


and Ride Time
```{r}
 ggplot(data = Cyclistic) +
     geom_col(mapping= aes(x = Ride_Time, y= Count, fill=member_casual), position="dodge")
```

I wanted to show a progression of usage times, but initially, the data formed in ascending order when it came to the x axis intervals. To remedy this, I used used the levels funtion to format the x axis to what I wanted to present.
```{r}
Cyclistic$Ride_Time<-factor(Cyclistic$Ride_Time, levels = c("Under 10", "10 to 30", "30 to 60", "Over 60"))
```
When I saw that my data displayed how I wanted, I enhanced the visual by adding titles and labels to make the data presented more clear.
```{r}
ggplot(data = Cyclistic) +
  geom_col(mapping = aes(x = Ride_Time, y = Count, fill = member_casual), 
           position = "dodge") +
  scale_fill_manual(values = member_colors,
                    labels = c("Casual Riders", "Annual Members")) +
  labs(title = "Ride Duration: Members vs Casual Riders",
       subtitle = "Distribution of trip lengths by rider type",
       x = "Ride Duration (minutes)",
       y = "Number of Riders",
       fill = "Rider Type") +
  theme(legend.position = "top")
```

## Analyzing my visualizations

### "How do annual members and casual riders use Cyclistic bikes differently?"

Since we are attempting to solve how to convert casual riders to annual members, Looking at the breakdowns from my previous visualizations was important to see what trends that I could identify in comparison to member and casuals. 
 
#### Looking at Usage Data

When looking at the monthly breakdown visual, I see that the data between members and casuals has a similar trend. However, when looking at the Day Usage data, there is a different story being told. 

When it comes to members, we see that the use during the weekdays is higher than that of the weekend. This can be an indicator that members are using the bikes to commute to and from work. With that in mind, I look at the Time of Day the bikes are being used. This confirms my suspicion that the members are using bikes to commute to and from work as the times of use spike from 6 a.m. to 8 a.m. and again around 4 p.m. to 6 p.m.

Whereas when it comes to casual riders, the data shows a different story. The usage of bikes are higher on the weekends *although not by much* and the time of day shows that the majority of use is mid-day (11 a.m. and starts to trend downward around 4 p.m.). This shows that usage is likely to be tourists or for leisure.

## Conclusion

My analysis shows that annual members and casual riders use Cyclystic bikes differently. I found that members primarily use Cyclystic bikes during the weekday during commute hours most likely for daily transportation. In contrast, casual riders use Cyclystic bikes during weekends and midday hours most likely for leisure and recreation.

## Recommendation for Conversion

### After analyzing the data, I would recommend the following to convert casual riders to annual members:

* Flexible membership options that cater to recreational/occasional riders
* Emphasize cost savings and convenience for frequent riders
* An introduction of seasonal and weekend promotions to encourage repeat use with membership benefits
