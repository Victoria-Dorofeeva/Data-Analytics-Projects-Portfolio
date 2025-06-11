# Business task: analyze users' historical trip data to find out how annual members and casual riders use Cyclistic bikes differently in order to inform the design of the new marketing strategy aimed at converting casual users into annual, more profitable users
# Data sources: 2 files with historical data: data_2019 and data_2020
## First I install all necessary R packages
	install.packages("tidyverse")
	install.packages("readr")
# Then I load these packages
	library(tidyverse)
	library(readr)
	library(dplyr)
	library(tidyr)
# Now I access csv files with historical trip data via readr package and save them as data frames data_2019 and data_2020
	data_2019 <- read_csv("C:/Users/baste/Downloads/Divvy_Trips_2019_Q1.csv")
	data_2020 <- read_csv("C:/Users/baste/Downloads/Divvy_Trips_2020_Q1.csv")
# Next I save the data frames as tibble for more streamlined work with data frames
	data_2019_tibble <- as_tibble(data_2019)
	data_2020_tibble <- as_tibble(data_2020)
# Now I start studying the contents of the data frames by learning how many and what kind of columns each data frame has with colnames function and fetching a quick summary of the data frames with glimpse function. I learn that the tables have different amount of columns and inconsistent column names, which means I will have some cleaning and manipulation to do before aggregating data into one data frame
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
# Finally, I use View function to take a look at the tables and see how they are organized. Before actually analyzing data to answer the business task question I want to aggregate all data into one data frame. In order to do that, I need to identify columns from each data frame that I want to bring into an aggregated data frame, and calculate and add columns if needed. At this step I start thinking which column I should investigate further
	View(data_2019_tibble)
	View(data_2020_tibble)
# I inquire whether there are several bike types (how many unique values in column rideable_type of data_2020 data frame). Turns out, there is only one type of bike, so my idea to investigate if members and casual users tend to use different types of bikes won't be needed, which is good because we do not have a corresponding column in data_2019 data frame
	table(data_2020_tibble$rideable_type)
	docked_bike
 426887
# Having studied both 2019 and 2020 tables, I come up with the following subquestions to answer the main question of how member and casual bike user behaviors differ:
	1. How does trip duration differ between members and casueal users?
	2. How does start day of the week differ between members and casueal users?
	3. What are the most popular departing stations among members and casueal users?
# I notice that data_2020_tibble does not have a column calculating trip duration, so I need to create it. Before I do that, I check whether trip duration in data_2019_tibble is in min or sec in order to count it in data_2020_tibble. I count it using interval function and put into trip_duration_check column, and save this as a new data frame trip_duration_calc for easier access
	trip_duration_calc <- data_2019_tibble %>%
	mutate(start_time = ymd_hms(start_time), end_time = ymd_hms(end_time)) %>% 
	mutate(trip_duration_check = interval(start_time, end_time) / seconds(1))
# Then I need to compare the numbers in trip_duration_check column and tripduration column which came with the data set to make sure the numbers match. Table function helps me see how many rows match (TRUE) and do not match (FALSE) without scrolling through thousands of rows
	trip_duration_calc$tripduration <- as.numeric(trip_duration_calc$tripduration)
	trip_duration_calc$trip_duration_check <- as.numeric(trip_duration_calc$trip_duration_check)
	table(trip_duration_calc$tripduration == trip_duration_calc$trip_duration_check)
	 FALSE   TRUE 
	 24      365045
# The code revals that the vast majority of rows match, however, there are 24 rows that do not match. I decide to look closer at these 24 rows. I create a column with boolean data type called boolean_trip_dur in trip_duration_calc data frame
	trip_duration_calc$bolean_trip_dur <- trip_duration_calc$tripduration == trip_duration_calc$trip_duration_check
# I notice I misspelled boolean and need ro rename the column and save it to trip_duration_calc data frame
	trip_duration_calc <- rename(trip_duration_calc, boolean_trip_dur = bolean_trip_dur)
# Then I create a new data frame with selected columns from trip_duration_calc to study each row in detail
	new_bool_df <- trip_duration_calc %>% select('start_time', 'end_time', 'tripduration', 'trip_duration_check', 'boolean_trip_dur')
# I filter for only FALSE values in boolean_trip_dur column. Then I use View and print(n=...) functions to see the whole table
	View(filter(new_bool_df, boolean_trip_dur == "FALSE"))
	print(filter(new_bool_df, boolean_trip_dur == "FALSE"), n=24)
![image](https://github.com/user-attachments/assets/83063fb5-d4a6-4174-b8df-53c498b2e348)
# The table reveals that 24 out of 365059 values in the tripduration column that came with the file for this case study are not matching my calculations in trip_duration_check column. I cannot find any problems with columns and data types, so I count a few random trip durations manually. The results match my calculations in trip_duration_check column. I therefore decide that these must be typos and decide to use my trip_duration_check column instead of tripduration for further analysis
# 
