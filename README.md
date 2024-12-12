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

- Trip details (fare, distance, and duration)
- Passenger demographics (new vs. repeat passengers)
- Ratings (passenger and driver)
- Monthly and city-level targets

# Ad-Hoc Requests

- City-Level Fare and Trip Summary Report<br>

SQL Code:With total_trips as(SELECT<br>
         c.city_name as city_name,<br>
         t.city_id  as city_id,<br>
         COUNT(t.trip_id) AS total_trips,<br>
         SUM(t.fare_amount) AS fare_amount,<br>
         SUM(t.distance_travelled_km) AS distance_travelled<br>
    FROM fact_trips t<br>
	JOIN dim_city c<br> 
    ON t.city_id = c.city_id<br>
    GROUP BY t.city_id),<br> 
trip_fare as<br> 
(SELECT<br> 
      city_name,<br>
      total_trips,<br>
      (fare_amount/distance_travelled) as avg_fare_per_km,<br>
      (fare_amount/total_trips) as avg_fare_per_trip<br>
FROM total_trips )<br>
SELECT *,<br>
(total_trips*100/SUM(total_trips) OVER ()) as pct_contribution_to_total_trips<br> 
from trip_fare<br>


- Monthly City-Level Trips Target Performance
  
SQL Code: WITH target As (SELECT<br>
    city_id,<br>
	monthname(month) as month_name,<br>
    total_target_trips as target_trip<br>
FROM targets_db.monthly_target_trips),<br>
actual As (SELECT<br>
    city_id,<br>
	monthname(date) as month_name,<br>
    count(trip_id) as actual_trips<br> 
FROM trips_db.fact_trips<br>
GROUP BY city_id, month_name)<br>
Select<br>
	c.city_name, t.month_name,<br> 
    actual_trips,<br> 
    target_trip,<br>
    CASE<br>
		When actual_trips > target_trip Then "Above_Target"<br>
        else "Below_target"<br>
    END as performance_status,<br>
    ROUND((actual_trips-target_trip)*100/target_trip,2) as pct_difference<br>
FROM target t<br>
JOIN actual a<br> 
ON t.city_id = a.city_id and t.month_name = a.month_name<br>
JOIN dim_city c<br> 
ON t.city_id = c.city_id<br>


- Repeat Passenger Trip Frequency by City
  
SQL Code:SELECT<br>
    c.city_name,<br>
    ROUND(SUM(CASE WHEN t.trip_count = '2-Trips' THEN t.repeat_passenger_count ELSE 0 END) * 100.0 / SUM(t.repeat_passenger_count),2) AS "2-Trips_pct",<br>
    ROUND(SUM(CASE WHEN t.trip_count = '3-Trips' THEN t.repeat_passenger_count ELSE 0 END) * 100.0 / SUM(t.repeat_passenger_count),2) AS "3-Trips_pct",<br>
    ROUND(SUM(CASE WHEN t.trip_count = '4-Trips' THEN t.repeat_passenger_count ELSE 0 END) * 100.0 / SUM(t.repeat_passenger_count),2) AS "4-Trips_pct",<br>
    ROUND(SUM(CASE WHEN t.trip_count = '5-Trips' THEN t.repeat_passenger_count ELSE 0 END) * 100.0 / SUM(t.repeat_passenger_count),2) AS "5-Trips_pct",<br>
    ROUND(SUM(CASE WHEN t.trip_count = '6-Trips' THEN t.repeat_passenger_count ELSE 0 END) * 100.0 / SUM(t.repeat_passenger_count),2) AS "6-Trips_pct",<br>
    ROUND(SUM(CASE WHEN t.trip_count = '7-Trips' THEN t.repeat_passenger_count ELSE 0 END) * 100.0 / SUM(t.repeat_passenger_count),2) AS "7-Trips_pct",<br>
    ROUND(SUM(CASE WHEN t.trip_count = '8-Trips' THEN t.repeat_passenger_count ELSE 0 END) * 100.0 / SUM(t.repeat_passenger_count),2) AS "8-Trips_pct",<br>
    ROUND(SUM(CASE WHEN t.trip_count = '9-Trips' THEN t.repeat_passenger_count ELSE 0 END) * 100.0 / SUM(t.repeat_passenger_count),2) AS "9-Trips_pct",<br>
    ROUND(SUM(CASE WHEN t.trip_count = '10-Trips' THEN t.repeat_passenger_count ELSE 0 END) * 100.0 / SUM(t.repeat_passenger_count),2) AS "10-Trips_pct"<br>
FROM<br>
    dim_repeat_trip_distribution t<br>
JOIN<br> 
    dim_city c<br>
ON<br>
    t.city_id = c.city_id<br>
GROUP BY<br> 
    c.city_name;<br>
  
- Cities with Highest and Lowest Total New Passengers
  
SQL Code:WITH total_passenegers_summary As (SELECT<br> 
	c.city_name,<br>
	SUM(p.new_passengers) as total_new_passengers<br>
FROM fact_passenger_summary p<br>
JOIN dim_city c<br> 
ON p.city_id = c.city_id<br> 
GROUP BY city_name),<br>
rank_cities as ( SELECT<br> 
              city_name,<br>
              total_new_passengers,<br>
              RANK () OVER(ORDER BY total_new_passengers DESC) as rank_high,<br>
              RANK () OVER(ORDER BY total_new_passengers ASC) as rank_low<br>
              FROM total_passenegers_summary)<br>
SELECT<br> 
        city_name,<br>
        total_new_passengers,<br>
        CASE<br>
            WHEN rank_high<=3 THEN 'Top_3'<br>
            WHEN rank_low <=3 THEN 'Bottom_3'<br>
            ELSE NULL<br>
        END AS city_category<br>
FROM rank_cities<br>
WHERE<br> 
    rank_high <= 3 OR rank_low <= 3<br>
ORDER BY<br> 
    city_category, total_new_passengers DESC;<br>
  

- Identify month with Highest Revenue Month for Each City
  
SQL Code:WITH total_revenue as (SELECT<br>
      c.city_name,<br>
      monthname(date) as highest_revenue_month,<br>
      SUM(t.fare_amount) as total_revenue<br>
FROM fact_trips t<br>
JOIN dim_city c<br>
ON t.city_id = c.city_id<br> 
GROUP BY c.city_name, monthname(date)<br>
),<br>
Highest_revenue_month As(<br>
SELECT city_name,<br> 
       highest_revenue_month,<br> 
       total_revenue,<br> 
       RANK() Over(Partition by city_name Order by total_revenue DESC) as ranked<br>
FROM total_revenue)<br>
SELECT city_name,<br> 
	   highest_revenue_month,<br> 
       total_revenue,<br>
       ROUND((total_revenue)*100/(SUM(total_revenue) Over()),2) as pct_contribution<br> 
FROM Highest_revenue_month<br>

- Monthly and City-Wide Repeat Passenger Rate
  
SQL Code:WITH passenger_count as (SELECT<br> 
      c.city_name,<br> 
      monthname(s.month) as month_name,<br>
      SUM(s.total_passengers) as total_passengers,<br>
      SUM(s.repeat_passengers) as repeat_passengers<br>
FROM fact_passenger_summary s<br>
JOIN dim_city c<br>
on s.city_id = c.city_id<br>
group by c.city_name, monthname(s.month)),<br>
month_wise_passengers as (SELECT<br> 
	  city_name,<br> 
      month_name,<br>
      total_passengers,<br>
      repeat_passengers,<br>
      ROUND((repeat_passengers*100/total_passengers),2) as monthly_repeat_passenger_rate,<br>
      SUM(repeat_passengers) OVER(partition by city_name) as city_wise_repeat_passengers<br>
FROM passenger_count)<br>
SELECT<br> 
      city_name,<br> 
      city_name,<br> 
      month_name,<br>
      total_passengers,<br>
      repeat_passengers,<br>
      monthly_repeat_passenger_rate,<br>
      ROUND((city_wise_repeat_passengers*100)/total_passengers,2) as city_repeat_passenger_rate<br> 
FROM month_wise_passengers<br>

# Insights and Recommendations
  
- Surat and Lucknow exhibit the highest repeat passenger loyalty, while Mysore and Jaipur show the lowest.
- May and June have the highest RPR%, while January and February are the lowest, indicating seasonal variations.
- Tailored marketing efforts and partnerships can improve passenger engagement in low-performing cities.


