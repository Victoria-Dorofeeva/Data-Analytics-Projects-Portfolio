# Business task: analyze users' historical trip data to find out how annual members and casual riders use Cyclistic bikes differently in order to inform the design of the new marketing strategy aimed at converting casual users into annual, more profitable users
## Data sources: 2 files with historical data: data_2019 and data_2020
## Platform: R Studio, R programming language
# 1. Data preparation and manupulation
## First I install all necessary R packages
	install.packages("tidyverse")
	install.packages("readr")
## Then I load these packages
	library(tidyverse)
	library(readr)
	library(dplyr)
	library(tidyr)
## Now I access csv files with historical trip data via readr package and save them as data frames data_2019 and data_2020
	data_2019 <- read_csv("C:/Users/baste/Downloads/Divvy_Trips_2019_Q1.csv")
	data_2020 <- read_csv("C:/Users/baste/Downloads/Divvy_Trips_2020_Q1.csv")
## Next I save the data frames as tibble for more streamlined work with data frames
	data_2019_tibble <- as_tibble(data_2019)
	data_2020_tibble <- as_tibble(data_2020)
## Now I start studying the contents of the data frames by learning how many and what kind of columns each data frame has with colnames function and fetching a quick summary of the data frames with glimpse function. I learn that the tables have different amount of columns and inconsistent column names, which means I will have some cleaning and manipulation to do before aggregating data into one data frame
	colnames(data_2019_tibble)
	[1] "trip_id"           "start_time"        "end_time"          "bikeid"            "tripduration"     
	[6] "from_station_id"   "from_station_name" "to_station_id"     "to_station_name"   "usertype"         
	[11] "gender"            "birthyear"       
	colnames(data_2020_tibble)
	[1] "ride_id"            "rideable_type"      "started_at"         "ended_at"           "start_station_name"
	[6] "start_station_id"   "end_station_name"   "end_station_id"     "start_lat"          "start_lng"         
	[11] "end_lat"            "end_lng"            "member_casual"
	glimpse(data_2019_tibble)
	glimpse(data_2020_tibble)
## Finally, I use View function to take a look at the tables and see how they are organized. Before actually analyzing data to answer the business task question I want to aggregate all data into one data frame. In order to do that, I need to identify columns from each data frame that I want to bring into an aggregated data frame, and calculate and add columns if needed. At this step I start thinking which column I should investigate further
	View(data_2019_tibble)
 ![image](https://github.com/user-attachments/assets/3d24e557-be79-4f03-a9a4-638af05a47e4)
	View(data_2020_tibble)
 ![image](https://github.com/user-attachments/assets/8afb4e70-bf41-46e2-b44e-e13d6e7b13b8)
## I inquire whether there are several bike types (how many unique values in column rideable_type of data_2020 data frame). Turns out, there is only one type of bike, so my idea to investigate if members and casual users tend to use different types of bikes won't be needed, which is good because we do not have a corresponding column in data_2019 data frame
	table(data_2020_tibble$rideable_type)
	docked_bike
	426887
## Having studied both 2019 and 2020 tables, I come up with the following subquestions to answer the main question of how member and casual bike user behaviors differ:
	1. How does trip duration differ between members and casual users?
	2. How does start day of the week differ between members and casual users?
	3. What are the most popular departure stations among members and casual users?
 	4. What are the most popular arrival stations among members and casual users?
 	5. Is there a significant amount of trips that end where they started by yser type?
## I notice that data_2020_tibble does not have a column calculating trip duration, so I need to create it. Before I do that, I check whether trip duration in data_2019_tibble is in min or sec in order to count it in data_2020_tibble. I count it using interval function and put into trip_duration_check column, and save this as a new data frame trip_duration_calc for easier access
	trip_duration_calc <- data_2019_tibble %>%
	mutate(start_time = ymd_hms(start_time), end_time = ymd_hms(end_time)) %>% 
	mutate(trip_duration_check = interval(start_time, end_time) / seconds(1))
## Then I need to compare the numbers in trip_duration_check column and tripduration column which came with the data set to make sure the numbers match. Table function helps me see how many rows match (TRUE) and do not match (FALSE) without scrolling through thousands of rows
	trip_duration_calc$tripduration <- as.numeric(trip_duration_calc$tripduration)
	trip_duration_calc$trip_duration_check <- as.numeric(trip_duration_calc$trip_duration_check)
	table(trip_duration_calc$tripduration == trip_duration_calc$trip_duration_check)
	 FALSE   TRUE 
	 24      365045
## The code revals that the vast majority of rows match, however, there are 24 rows that do not match. I decide to look closer at these 24 rows. I create a column with boolean data type called boolean_trip_dur in trip_duration_calc data frame
	trip_duration_calc$bolean_trip_dur <- trip_duration_calc$tripduration == trip_duration_calc$trip_duration_check
## I notice I misspelled boolean and need ro rename the column and save it to trip_duration_calc data frame
	trip_duration_calc <- rename(trip_duration_calc, boolean_trip_dur = bolean_trip_dur)
## Then I create a new data frame with selected columns from trip_duration_calc to study each row in detail
	new_bool_df <- trip_duration_calc %>% select('start_time', 'end_time', 'tripduration', 'trip_duration_check', 'boolean_trip_dur')
## I filter for only FALSE values in boolean_trip_dur column. Then I use View and print(n=...) functions to see the whole table
	View(filter(new_bool_df, boolean_trip_dur == "FALSE"))
	print(filter(new_bool_df, boolean_trip_dur == "FALSE"), n=24)
![image](https://github.com/user-attachments/assets/83063fb5-d4a6-4174-b8df-53c498b2e348)
## The table reveals that 24 out of 365059 values in the tripduration column that came with the file for this case study are not matching my calculations in trip_duration_check column. I cannot find any problems with columns and data types, so I count a few random trip durations manually. The results match my calculations in trip_duration_check column. I therefore decide that these must be typos and decide to use my trip_duration_check column instead of tripduration for further analysis
## I add trip_duration_check column to original data_2019_tibble data frame for convinience and further study
	data_2019_tibble <- mutate(trip_duration_calc, trip_duration_check)
## I notice that column boolean_trip_dur was accidentally added to data_2019_tibble data frame at some point so I remove it
	data_2019_tibble$boolean_trip_dur <- NULL
## Next I want to add a column to data_2019_tibble data frame that shows on which day of the week each trip started
	data_2019_tibble$start_day <- weekdays(data_2019_tibble$start_time)
## Now I add necessary columns to data_2020_tibble data frame: trip duration and day of the week
	data_2020_tibble <- data_2020_tibble %>%
	+ mutate(started_at = ymd_hms(started_at), ended_at = ymd_hms(ended_at)) %>%
	+ mutate(trip_duration = interval(started_at, ended_at) / seconds(1))
	
 	data_2020_tibble$start_day <- weekdays(data_2020_tibble$started_at)
## Before merging 2 data frames with the data for 2019 and 2020, I need to make column names consistent
	data_2020_tibble <- rename(data_2020_tibble, start_time = started_at)
 	data_2020_tibble <- rename(data_2020_tibble, end_time = ended_at)
  
	data_2019_tibble <- rename(data_2019_tibble, trip_duration = trip_duration_check)
 	data_2019_tibble <- rename(data_2019_tibble, start_station_name = from_station_name)
  	data_2019_tibble <- rename(data_2019_tibble, end_station_name = to_station_name)
## Values of user_type column in data_2019_tibble also need to be replaced to be consistent with data_2020_tibble
	data_2019_tibble <- rename(data_2019_tibble, user_type = usertype)
	data_2020_tibble <- rename(data_2020_tibble, user_type = member_casual)

 	data_2019_tibble$user_type <- replace(data_2019_tibble$user_type, data_2019_tibble$user_type == "Subscriber", "casual")
	data_2019_tibble$user_type <- replace(data_2019_tibble$user_type, data_2019_tibble$user_type == "Customer", "member")
## I remove not needed columns from both data frames
 	data_2019_tibble <- subset(data_2019_tibble, select = c(-trip_id, -bikeid, -tripduration, -from_station_id, -to_station_id, -gender, -birthyear))
  	data_2020_tibble <- subset(data_2020_tibble, select = c(-ride_id, -rideable_type, -start_station_id, -end_station_id, -start_lat, -start_lng, -end_lat, -end_lng))
## Now I want to combine my 2 data frames vertically
	merged_df <- rbind(data_2019_tibble, data_2020_tibble)
 ![image](https://github.com/user-attachments/assets/5f805eb9-f525-4f92-b458-47a2b5e4afee)
# 2. Analysis and visualizations
## 1) I answer the first question (How does trip duration differ between members and casual users?) by counting mean of trip duration by user type
	merged_df %>%
	+ group_by(user_type) %>%
	+ summarise(mean_trip_dur = mean(trip_duration))
![image](https://github.com/user-attachments/assets/f72ce987-c958-4748-a0e1-c307c2fd9b05)
## 2) Then I answer the second question (How does start day of the week differ between members and casual users?) by find mode of the start day of the week. First, I need to create mode function
	mode <- function(x, na.rm = FALSE) {     
	if(na.rm){
	x = x[!is.na(x)]
	}
	val <- unique(x)
	return(val[which.max(tabulate(match(x, val)))])
 	}

  	merged_df %>%
	group_by(user_type) %>%
	summarise(mode_start_day = mode(start_day))
![image](https://github.com/user-attachments/assets/ed6a9ae0-7734-4a47-a044-1fac8b2ef215)
## I also want to present these findings as a stacked bar chart, to see the whole week and potentially a trend
	ggplot(data = merged_df) + geom_bar(mapping = aes(x=start_day, fill=user_type))	
![image](https://github.com/user-attachments/assets/6535a731-3d57-4abf-b4c9-f45d91028cf0)
## I see that the days of the week are in alphabetic order instead of week order, so I use factor function to specify levels I want for the values and rerun previous code
	merged_df$start_day <- factor(merged_df$start_day, c("Monday", "Tuesday", "Wednesday", "Thursday","Friday","Saturday", "Sunday"))
 
	ggplot(data = merged_df) + geom_bar(mapping = aes(x=start_day, fill=user_type))
![image](https://github.com/user-attachments/assets/a7505bdf-b144-4015-a066-0981cea1761a)
## Much better but the graph still looks confusing, so I divide the graph into 2 by user type via facet_wrap
	ggplot(data = merged_df) + geom_bar(mapping = aes(x=start_day, fill=user_type)) + facet_wrap(~user_type)
 ![image](https://github.com/user-attachments/assets/26ceb112-6b6b-4257-b655-1c162f18a1ab)
## For the final touch, I rotate x-axis labels so that they are more visible and add title and subtitle
	ggplot(data = merged_df) + geom_bar(mapping = aes(x=start_day, fill=user_type)) + facet_wrap(~user_type) + theme(axis.text.x = element_text(angle = 45)) + labs(title="Days of the week casual and member users prefer to start their trips", subtitle="Based on 2019 and 2020 data")
![Rplot](https://github.com/user-attachments/assets/6c52e1d2-8434-43fe-8e5d-4ed738037095)
## 3) To answer the third question (What are the most popular departure stations among members and casual users?) I create 2 data frames as subsets containing only member or only casual users respectively and use sort and table functions. I can see that only 1 station among most popular departure stations matches between member and casual users
	subset_member <- subset(merged_df, user_type == "member")	
	sort(table(subset_member$end_station_name), decreasing = TRUE)[1:10]
![image](https://github.com/user-attachments/assets/7746f431-f437-43f1-aa73-265eed9a85fc)

	subset_casual <- subset(merged_df, user_type == "casual")
	sort(table(subset_casual$end_station_name), decreasing = TRUE)[1:10]
![image](https://github.com/user-attachments/assets/c630d4fa-d2ba-4a1f-bdd1-26bbb2d4c646)
## 4) To answer the fourth question (What are the most popular arrival stations among members and casual users?) I repeat the code for end_station_name. I can see that only 1 station among most popular arrival stations matches between member and casual users
 	sort(table(subset_member$start_station_name), decreasing = TRUE)[1:10]
![image](https://github.com/user-attachments/assets/b30cb751-4cdc-4339-b62d-e0b4390e058c)

 	sort(table(subset_casual$start_station_name), decreasing = TRUE)[1:10]
![image](https://github.com/user-attachments/assets/1f64bd6c-aca2-4415-943c-75891ee87576)
## 5) To answer the final fifth question (Is there a significant amount of trips that end where they started by yser type?) I apply boolean logic. I also want to find % of the trips that started and ended in the same location for member and casual users respectively. I see that it is 2% for members and 20% for causual users
	table(subset_member$start_station_name == subset_member$end_station_name)
 	FALSE   TRUE 
	706399  13914

 	x <- 13914*100/(706399+13914)
  	print(x)
	[1] 1.93166
 
 	table(subset_casual$start_station_name == subset_casual$end_station_name)
  	FALSE  TRUE 
	58165  13477 

	y <- 13477*100/(58165+13477)
 	print(y)
	[1] 18.81159
# 3. Results and cocnlusions
