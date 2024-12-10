# Goodcabs

GoodCabs is a comprehensive analysis project aimed at enhancing the efficiency of a cab service provider. This project focuses on identifying key operational metrics, understanding performance trends, and offering actionable insights to optimize cab services.

# Project Overview

This project focuses on analyzing cab service data to address challenges related to:
- Repeat Passenger Rate (RPR%)
- Customer Satisfaction
- Trip Volume
- Revenue Growth
- The insights aim to help stakeholders make data-driven decisions to optimize operations and enhance service quality.


# Problem Statement

GoodCabs is a prominent cab service company operating in 10 tier-2 cities in India. This project analyzes key performance metrics to support GoodCabs' mission of enhancing passenger satisfaction and achieving sustainable growth by 2024.GoodCabs faces challenges in tracking operational performance and meeting monthly trip targets. The lack of consolidated metrics makes it difficult to analyze driver efficiency, passenger behavior, and trip demand trends. The goal of this project is to provide a dashboard with actionable insights that empower decision-making.

# Key Features

- Interactive Dashboards: A well-structured dashboard visualizing key performance metrics-
- Metrics Covered:
- Total trips completed.
- Driver efficiency (trips per driver).
- Passenger engagement (repeat and new customers).
- Monthly trip targets vs. actuals.
- Performance Tracking: Real-time monitoring of operational metrics.
- Data Insights: Identification of trends and areas for improvement.
  
# Tools & Technologies

- Data Processing: SQL.
- Visualization: Power BI/Tableau for dashboard creation.
- Data Sources: Raw trip, driver, and target data provided by GoodCabs.

# The dataset includes:

Trip details (fare, distance, and duration)
Passenger demographics (new vs. repeat passengers)
Ratings (passenger and driver)
Monthly and city-level targets

# Ad-Hoc Requests

- City-Level Fare and Trip Summary Report
SQL Code:With total_trips as(SELECT 
        c.city_name as city_name,
        t.city_id  as city_id,
        COUNT(t.trip_id) AS total_trips,
        SUM(t.fare_amount) AS fare_amount,
        SUM(t.distance_travelled_km) AS distance_travelled
    FROM fact_trips t
	JOIN dim_city c 
    ON t.city_id = c.city_id
    GROUP BY t.city_id), 
trip_fare as 
(SELECT 
      city_name, 
      total_trips,
      (fare_amount/distance_travelled) as avg_fare_per_km,
      (fare_amount/total_trips) as avg_fare_per_trip
FROM total_trips )
SELECT *,
(total_trips*100/SUM(total_trips) OVER ()) as pct_contribution_to_total_trips 
from trip_fare


- Monthly City-Level Trips Target Performance
SQL Code:

- Repeat Passenger Trip Frequency by City
SQL Code:
  
- Cities with Highest and Lowest Total New Passengers
SQL Code:
  

- Highest Revenue Month for Each City
SQL Code:

- Monthly and City-Wide Repeat Passenger Rate
SQL Code:

# Dashboard 



# Insights and Recommendations
  
- Surat and Lucknow exhibit the highest repeat passenger loyalty, while Mysore and Jaipur show the lowest.
- May and June have the highest RPR%, while January and February are the lowest, indicating seasonal variations.
- Tailored marketing efforts and partnerships can improve passenger engagement in low-performing cities.


