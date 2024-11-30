<h2 align="center">
 <img src="https://github.com/user-attachments/assets/22d45d68-1868-496f-87c2-4258115d9c9f" alt="Live Project" width="100"> <br>
  <br>
  Codebasics Resume Project Challenge #13
	<h3 align="center">
  Opearional Insights for GoodCabs Growth 2024
</h3>

</h2>




**Ad-Hoc Business Requests**
---

**Business Request 1: City-Level Fare and Trip Summary Report**  
Generate a report that summarizes the total trips, average fare per kilometer, average fare per trip, and the percentage contribution of each city's trips to the total trip volume. This will provide insights into trip volume, pricing efficiency, and each city's overall contribution.  

**Fields:**  
- city name  
- total_trips  
- avg_fare_per_km  
- avg_fare_per_trip  
- %_contribution_to_total_trips  

```sql
SELECT 
    dc.city_name AS "City",
    COUNT(ft.trip_id) AS "Trips",
    ROUND(SUM(ft.fare_amount) / SUM(ft.distance_travelled_km), 2) AS "Average Fare per KM (₹)",
    ROUND(SUM(ft.fare_amount) / COUNT(ft.trip_id), 2) AS "Average Fare per Trip (₹)",
    ROUND(
        ((COUNT(ft.trip_id)) / 
        (SELECT COUNT(*) FROM fact_trips)* 100.0), 2
    ) AS "Contribution to Trips (%)"
FROM 
    fact_trips ft
JOIN 
    dim_city dc 
    ON ft.city_id = dc.city_id
GROUP BY 
    dc.city_name
ORDER BY 
    "Trips" DESC;
```

---

**Business Request 2: Monthly City-Level Trips Target Performance Report**  
Generate a report that evaluates monthly and city-level target performance for trips. For each city and month, compare the actual total trips with the target trips and categorize the performance as:  
- "Above Target" if actual trips exceed target trips  
- "Below Target" if actual trips are equal to or less than target trips  

Also, calculate the percentage difference between actual and target trips to quantify the performance gap.  

**Fields:**  
- city name  
- month name  
- actual trips  
- target_trips  
- performance status  
- % difference  

```sql
SELECT
    dc.city_name AS "City",
    DATE_FORMAT(dd.start_of_month, '%M %Y') AS "Month",
    COUNT(ft.trip_id) AS "Actual Trips",
    mtt.total_target_trips AS "Targeted Trips",
    CASE
        WHEN COUNT(ft.trip_id) > mtt.total_target_trips THEN 'Above Target'
        ELSE 'Below Target'
    END AS "Performance Status",
    ROUND(
        ((COUNT(ft.trip_id) - mtt.total_target_trips) / mtt.total_target_trips) * 100, 2
    ) AS "Difference (%)"
FROM 
    trips_db.fact_trips AS ft
JOIN 
    trips_db.dim_city AS dc 
    ON ft.city_id = dc.city_id
JOIN 
    trips_db.dim_date AS dd 
    ON dd.date = ft.date
JOIN
    targets_db.monthly_target_trips AS mtt
    ON ft.city_id = mtt.city_id
    AND dd.start_of_month = mtt.month
GROUP BY
    dc.city_name, dd.start_of_month, mtt.total_target_trips
ORDER BY 
    dd.start_of_month, dc.city_name;
```

---

**Business Request 3: City-Level Repeat Passenger Trip Frequency Report**  
Generate a report that shows the percentage distribution of repeat passengers by the number of trips they have taken in each city. Categorize repeat passengers by the number of trips taken (e.g., 2 trips, 3 trips, etc., up to 10 trips). This report helps identify cities with high repeat trip frequency, indicating strong customer loyalty or frequent usage patterns.  

**Fields:**  
- city name  
- 2-Trips  
- 3-Trips  
- 4-Trips  
- 5-Trips  
- 6-Trips  
- 7-Trips  
- 8-Trips  
- 9-Trips  
- 10-Trips  

```sql
SELECT 
    dc.city_name AS "City",
    ROUND(
        (SUM(CASE 
                 WHEN SUBSTRING_INDEX(drtd.trip_count, '-', 1) = "2" 
                 THEN drtd.repeat_passenger_count 
                 ELSE 0 
             END) * 100 / SUM(drtd.repeat_passenger_count)), 2
    ) AS "2-Trips",
    ROUND(
        (SUM(CASE 
                 WHEN SUBSTRING_INDEX(drtd.trip_count, '-', 1) = "3" 
                 THEN drtd.repeat_passenger_count 
                 ELSE 0 
             END) * 100 / SUM(drtd.repeat_passenger_count)), 2
    ) AS "3-Trips",
    ROUND(
        (SUM(CASE 
                 WHEN SUBSTRING_INDEX(drtd.trip_count, '-', 1) = "4" 
                 THEN drtd.repeat_passenger_count 
                 ELSE 0 
             END) * 100 / SUM(drtd.repeat_passenger_count)), 2
    ) AS "4-Trips",
    ROUND(
        (SUM(CASE 
                 WHEN SUBSTRING_INDEX(drtd.trip_count, '-', 1) = "5" 
                 THEN drtd.repeat_passenger_count 
                 ELSE 0 
             END) * 100 / SUM(drtd.repeat_passenger_count)), 2
    ) AS "5-Trips",
    ROUND(
        (SUM(CASE 
                 WHEN SUBSTRING_INDEX(drtd.trip_count, '-', 1) = "6" 
                 THEN drtd.repeat_passenger_count 
                 ELSE 0 
             END) * 100 / SUM(drtd.repeat_passenger_count)), 2
    ) AS "6-Trips",
    ROUND(
        (SUM(CASE 
                 WHEN SUBSTRING_INDEX(drtd.trip_count, '-', 1) = "7" 
                 THEN drtd.repeat_passenger_count 
                 ELSE 0 
             END) * 100 / SUM(drtd.repeat_passenger_count)), 2
    ) AS "7-Trips",
    ROUND(
        (SUM(CASE 
                 WHEN SUBSTRING_INDEX(drtd.trip_count, '-', 1) = "8" 
                 THEN drtd.repeat_passenger_count 
                 ELSE 0 
             END) * 100 / SUM(drtd.repeat_passenger_count)), 2
    ) AS "8-Trips",
    ROUND(
        (SUM(CASE 
                 WHEN SUBSTRING_INDEX(drtd.trip_count, '-', 1) = "9" 
                 THEN drtd.repeat_passenger_count 
                 ELSE 0 
             END) * 100 / SUM(drtd.repeat_passenger_count)), 2
    ) AS "9-Trips",
    ROUND(
        (SUM(CASE 
                 WHEN SUBSTRING_INDEX(drtd.trip_count, '-', 1) = "10" 
                 THEN drtd.repeat_passenger_count 
                 ELSE 0 
             END) * 100 / SUM(drtd.repeat_passenger_count)), 2
    ) AS "10-Trips"
FROM 
    dim_city AS dc
JOIN 
    dim_repeat_trip_distribution AS drtd
    ON dc.city_id = drtd.city_id    
GROUP BY
    dc.city_name
ORDER BY
    dc.city_name;

```

---

**Business Request 4: Identify Cities with Highest and Lowest Total New Passengers**  
Generate a report that calculates the total new passengers for each city and ranks them based on this value. Identify the top 3 cities with the highest number of new passengers and the bottom 3 cities with the lowest. Categorize them as "Top 3" or "Bottom 3" accordingly.  

**Fields:**  
- city name  
- total_new_passengers  
- city_category ("Top 3" or "Bottom 3")  

```sql
WITH RankCity AS (
    SELECT 
        dc.city_name,
        SUM(fps.new_passengers) AS total_new_passengers,
        RANK() OVER (ORDER BY SUM(fps.new_passengers) DESC) AS rank_desc,
        RANK() OVER (ORDER BY SUM(fps.new_passengers)) AS rank_asc
    FROM 
        trips_db.fact_passenger_summary fps
    JOIN 
        trips_db.dim_city dc ON fps.city_id = dc.city_id
    GROUP BY 
        dc.city_name
),
TopBottomCities AS (
    -- Filter for Top 3 and Bottom 3 cities
    SELECT 
        city_name,
        total_new_passengers,
        CASE 
            WHEN rank_desc <= 3 THEN 'Top 3'
            WHEN rank_asc <= 3 THEN 'Bottom 3'
        END AS city_category
    FROM 
        RankCity
        WHERE 
        rank_desc <= 3 OR rank_asc <= 3
)
-- Final result
SELECT 
    city_name as "City",
    total_new_passengers as "New Passengers",
    city_category as "City Category"
FROM 
    TopBottomCities
ORDER BY 
	total_new_passengers DESC;
```

---

**Business Request 5: Identify Month with Highest Revenue for Each City**  
Generate a report that identifies the month with the highest revenue for each city. Display the month name, revenue amount for that month, and the percentage contribution of that month's revenue to the city's total revenue.  

**Fields:**  
- city_name  
- highest revenue month  
- revenue  
- percentage contribution (%)  

```sql
WITH MonthWiseRevenue AS (
    SELECT
        dc.city_name AS city,
        DATE_FORMAT(dd.start_of_month, '%M %Y') AS month,
        SUM(ft.fare_amount) AS revenue
    FROM 
        fact_trips AS ft
    JOIN 
        dim_city AS dc
        ON ft.city_id = dc.city_id
    JOIN
        dim_date AS dd
        ON ft.date = dd.date
    GROUP BY
        dc.city_name, dd.start_of_month
),
RevenueContribution AS (
    SELECT
        *,
        ROUND((revenue / SUM(revenue) OVER (PARTITION BY city)) * 100, 2) AS revenue_contribution
    FROM
        MonthWiseRevenue
),
RankCity AS (
    SELECT
        *,
        DENSE_RANK() OVER (PARTITION BY city ORDER BY revenue DESC) AS rank_revenue,
        ROW_NUMBER() OVER (PARTITION BY city ORDER BY revenue DESC) AS row_num
        -- ROW_NUMBER is used to remove duplicate rows for cities and months
        -- where multiple months have identical revenue contributions
    FROM
        RevenueContribution
)
SELECT
    city AS "City",
    month AS "Month",
    revenue AS "Revenue (₹)",
    revenue_contribution AS "Revenue Contribution (%)"
FROM
    RankCity
WHERE
    rank_revenue = 1 AND row_num = 1
ORDER BY
    revenue DESC;
```

---

**Business Request 6: Repeat Passenger Rate Analysis**  
Generate a report that calculates two key metrics:  
1. **Monthly Repeat Passenger Rate:** The repeat passenger rate for each city and month, calculated by comparing repeat passengers to the total number of passengers.
**Fields:**  
- city name  
- month  
- total_passengers  
- repeat_passengers  
- monthly_repeat_passenger_rate (%): Repeat passenger rate at the city and month level  

```sql
select
	dc.city_name as "City",
    DATE_FORMAT(fps.month, '%M %Y') as "Month",
    fps.total_passengers as "Passengers",
    fps.repeat_passengers as "Repeat Passengers",
    round((fps.repeat_passengers/fps.total_passengers)*100, 2) "Repeat Rate (%)"
from fact_passenger_summary as fps
join dim_city as dc
on fps.city_id = dc.city_id
order by 
	dc.city_name
```
2. **City-wide Repeat Passenger Rate:** The overall repeat passenger rate for each city, calculated by aggregating repeat passenger data across all months.  
**Fields:**  
- city name  
- total_passengers  
- repeat_passengers  
- city_repeat_passenger_rate (%): Overall repeat passenger rate for each city  

```sql
WITH CityWisePassengers AS (
    SELECT
        dc.city_name AS city,
        SUM(fps.total_passengers) AS passengers,
        SUM(fps.repeat_passengers) AS repeat_passengers
    FROM 
        fact_passenger_summary AS fps
    JOIN 
        dim_city AS dc
        ON fps.city_id = dc.city_id
    GROUP BY
        dc.city_name
),
CityRepeatPassengerRate AS (
    SELECT
        city,
        passengers,
        repeat_passengers,
        ROUND((repeat_passengers * 100.0 / passengers), 2) AS city_repeat_passenger_rate
    FROM
        CityWisePassengers
)
SELECT
    city AS "City",
    passengers AS "Passengers",
    repeat_passengers AS "Repeat Passengers",
    city_repeat_passenger_rate AS "Repeat Rate (%)"
FROM
    CityRepeatPassengerRate
ORDER BY
    city_repeat_passenger_rate DESC;
```
---  


