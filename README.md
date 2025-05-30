# Business task: analyzing users' historical trip data find out how annual members and casual riders use Cyclistic bikes differently in order to inform the design of the new marketing strategy aimed at converting casual users into annual, more profitable users
# Data sources: 2 files with historical data: data_2019 and data_2020
# First I install all necessary R packages
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
# Now I start studying the contents of the data frames by learning how many and what kind of columns each data frame has with colnames function and fetching a quick summary of the data frames with glimpse function
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
# Finally, I use View function to take a look at the tables and see how they are organized. At this step I start thinking what I should investigate further
View(data_2019_tibble)
View(data_2020_tibble)
# I inquire whether there are several bike types (how many unique values in column rideable_type of data_2020 data frame). Turns out, there is only one type of bike, so my idea to investigate if members and casual users tend to use different types of bikes won't be needed
table(data_2020_tibble$rideable_type)
docked_bike
     426887
     
library(lubridate)
rm("difference") -- deleted accidentally created df

data_2019_tibble %>%
mutate(start_time = ymd_hms(start_time), end_time = ymd_hms(end_time)) %>% 
mutate(trip_duration_check = interval(start_time, end_time)) -- checking whether trip duration in data_2019 is in min or sec in order to count it in data_2020. We count it and put into trip_duration_check column 

trip_duration_calc$tripduration <- as.numeric(trip_duration_calc$tripduration)
trip_duration_calc$trip_duration_check <- as.numeric(trip_duration_calc$trip_duration_check)
table(trip_duration_calc$tripduration == trip_duration_calc$trip_duration_check) -- checking that the columns have identical entries
 FALSE   TRUE 
    24 365045 
trip_duration_calc$Bol_trip_dur <- trip_duration_calc$tripduration == trip_duration_calc$trip_duration_check  --adding column with Boolean answers to tripduration == trip_duration_check to trip_duration_calc df

new_bol_df <- trip_duration_calc %>% select('start_time', 'end_time', 'tripduration', 'trip_duration_check', 'Bol_trip_dur')

filter(new_bol_df, Bol_trip_dur == "FALSE") -- this tibble shows only 10 first rows

print(filter(new_bol_df, Bol_trip_dur == "FALSE"), n=24) -- to see all rows where Bol_trip_dur == "FALSE"
![image](https://github.com/user-attachments/assets/ce1eb030-7b52-49b5-8d05-5775edebecab)
