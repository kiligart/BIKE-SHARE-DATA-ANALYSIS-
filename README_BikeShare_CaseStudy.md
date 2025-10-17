# BIKE-SHARE-DATA-ANALYSIS-
This project explores how Cyclistic, a Chicago-based bike-share program, can turn casual riders into loyal annual members. Using 12 months of trip data, I analyzed ride patterns to uncover how members and casual users differ in their behavior — including ride duration, frequency, and weekday trends.

# 🚴‍♀️ Cyclistic Bike-Share Analysis  
**Case Study:** *How Does a Bike-Share Navigate Speedy Success?*  
**Tools:** RStudio (tidyverse, lubridate) | Tableau Public | GitHub  

---

## 🧭 1. Ask — Define the Business Task
Cyclistic, a Chicago-based bike-share company, aims to **increase annual memberships**.  
The marketing team believes that converting **casual riders into annual members** will drive long-term growth.  

**Business Question:**  
> How do annual members and casual riders use Cyclistic bikes differently?

**Goal:**  
Identify behavioral patterns and provide data-driven recommendations to help the marketing team design campaigns that encourage casual users to become members.

---

## 📦 2. Prepare — Data Source
I used **12 months of Divvy (Cyclistic) public trip data from 2024**, available at  
[https://divvy-tripdata.s3.amazonaws.com/index.html](https://divvy-tripdata.s3.amazonaws.com/index.html)

**Data overview:**
- Over 5.7 million rides  
- Fields include: `ride_id`, `rideable_type`, `started_at`, `ended_at`, `start_station_name`, `end_station_name`, `member_casual`, etc.  
- Each monthly `.csv` was combined into a single dataset for yearly analysis.  
- Personally identifiable information was excluded in accordance with the data license.

**ROCCC Check:**  
Reliable ✅ • Original ✅ • Comprehensive ✅ • Current ✅ • Cited ✅  

---

## 🧹 3. Process — Data Cleaning in R
**Tools:** `tidyverse`, `lubridate`, `janitor`  

### Cleaning & Transformation
```r
library(tidyverse)
library(lubridate)
library(janitor)

folder_path <- "/Users/mauri/OneDrive/Documents/notes/Bike_data_raw"
files <- list.files(path = folder_path, pattern = "2024.*\.csv$", full.names = TRUE)

all_trips <- files %>% map_df(~ read_csv(.x, show_col_types = FALSE)) %>%
  clean_names() %>%
  mutate(
    ride_length = as.numeric(difftime(ended_at, started_at, units = "mins")),
    day_of_week = wday(started_at, label = TRUE),
    month = month(started_at, label = TRUE, abbr = TRUE),
    hour = hour(started_at)
  ) %>%
  filter(ride_length > 1, ride_length < 1440)
```

**Steps performed:**
1. Combined 12 monthly datasets.  
2. Cleaned and standardized column names.  
3. Calculated new fields (`ride_length`, `day_of_week`, `month`, `hour`).  
4. Filtered invalid trips (shorter than 1 minute or longer than 24 hours).  
5. Exported cleaned data for Tableau visualization.

```r
write_csv(all_trips, "data/cleaned/all_trips_cleaned.csv")
```

---

## 📊 4. Analyze — Explore and Identify Trends

### Key Calculations
```r
# Ride stats by user type
summary_stats <- all_trips %>%
  group_by(member_casual) %>%
  summarise(
    total_rides = n(),
    avg_duration = mean(ride_length),
    median_duration = median(ride_length)
  )

# Weekly patterns
weekday_stats <- all_trips %>%
  group_by(member_casual, day_of_week) %>%
  summarise(
    number_of_rides = n(),
    avg_duration = mean(ride_length, na.rm = TRUE),
    .groups = "drop"
  )

# Monthly trends
monthly_stats <- all_trips %>%
  group_by(member_casual, month) %>%
  summarise(number_of_rides = n(), .groups = "drop")
```

---

## 📈 5. Share — Visualization in Tableau  
**Dashboard:** [View on Tableau Public →](https://public.tableau.com/app/profile/mauri.yuliana.sanchez.uribe/viz/BIKESHAREDASHBOARD/Dashboard1)

### Dashboard Highlights  
| Section | Visualization | Key Insight |
|----------|----------------|--------------|
| **KPI Cards** | Total rides, Avg duration, # Members, Busiest day | Overview of performance |
| **Line Chart** | Rides by hour | Members ride during commute hours; casuals ride midday |
| **Bar Chart** | Rides by weekday | Members consistent; casuals peak weekends |
| **Bar Chart** | Monthly trends | Summer peak in total rides |
| **Pie Chart** | Bike type preference | Classic and electric dominate |
| **Bar Chart (Top 10)** | Start/End stations | High-traffic hubs near tourist & commuting zones |

---

## 💡 6. Act — Recommendations
Based on the analysis:

1. **Promote memberships to weekend riders**  
   Most casual rides occur on weekends and in summer. Offering discounts or membership trials during these peaks could drive conversions.

2. **Highlight convenience for commuters**  
   Members ride more consistently on weekdays and during commuting hours. Marketing should emphasize reliability and time-saving benefits.

3. **Target popular stations with local ads**  
   Focus campaigns near high-traffic start and end stations (e.g., Streeter Dr & Grand Ave, DuSable Lake Shore Dr).

---

## 📚 7. Key Insights Summary
- **Members:** Regular, shorter weekday rides — consistent commuting pattern.  
- **Casual riders:** Longer leisure rides — mostly weekends and afternoons.  
- **Ride duration average:** ~15.7 minutes.  
- **Peak usage:** July–September.  
- **Most popular station:** Streeter Dr & Grand Ave.  

---

## 💾 Project Structure
```
├── data
│   ├── raw/                 # Monthly CSV files
│   └── cleaned/
│       ├── all_trips_cleaned.csv
│       ├── summary_stats.csv
│       ├── weekday_stats.csv
│       └── monthly_stats.csv
├── scripts/
│   └── data_cleaning.R
├── tableau/
│   └── dashboard.twbx
├── README.md
```

---

## 🧠 Learnings
- Reinforced skills in **data wrangling with tidyverse**.  
- Automated **data cleaning for multiple CSVs**.  
- Designed an **interactive Tableau dashboard** aligned with real business questions.  
- Delivered **data-driven recommendations** for marketing decisions.

---

## 📎 License
This project uses Divvy/Cyclistic data made available by **Motivate International Inc.** under the [Data License Agreement](https://www.divvybikes.com/data-license-agreement).
