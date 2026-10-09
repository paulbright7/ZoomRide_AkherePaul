-- MY TABLES
SELECT * FROM trips
SELECT * FROM drivers
SELECT * FROM customers



-- Q1 How many rows are in trips table

SELECT 
	COUNT(*) AS Total_Row
FROM trips;

-- Q2 Show the longest completed trips

SELECT 
	TOP 5 city, status, distance_km
FROM trips
WHERE status = 'completed'
ORDER BY distance_km DESC;

-- Q3. How many trips happened in each city

SELECT 
	city, COUNT(trip_Id) As total_trip
FROM trips
GROUP BY city;

-- Q4a. Find the duplicated trips

SELECT 
	trip_Id,customer_id,driver_id,city, trip_date, fare,  COUNT (*) AS duplicate
FROM trips
GROUP BY trip_Id, customer_id,driver_id,city, trip_date, fare
HAVING COUNT (*) > 1;

-- Q4b How many completed trips have a missing fare

SELECT 
	status, fare, COUNT(*) AS Missing_fare
FROM trips
WHERE status = 'completed' AND
		fare IS NULL
GROUP BY status, fare;

-- Q5 Fix the data.. Since we are reporting the missing fare and from our search there is no duplicated data, so the only thing need fixing is the spelling of the city and then trim to remove the space

UPDATE 
	trips
SET city = 'Port Harcourt'
WHERE city = 'PH';

UPDATE 
	trips
SET city = 'Port Harcourt'
WHERE city = 'Port-Harcourt';

UPDATE 
	trips
SET city = 'Nairobi'
WHERE city = 'Nairobbi';

UPDATE 
	trips
SET city = 'Kampala'
WHERE city = 'Kampla';

UPDATE 
	trips
SET city = TRIM(city);

-- Q6 Revenue by City that is how much each city made from completed trips

SELECT 
	city, COUNT(status) AS completed_trips, SUM(fare) AS Revenue, 
			ROUND(AVG(fare), 2) AS Average_revenue
FROM trips
WHERE status = 'completed'
GROUP BY city;

-- Q7 Revenue by Month

SELECT 
	CONVERT(CHAR(7), trip_date, 120) AS Month,
		COUNT(status) AS Number_of_trip, SUM(fare) AS Revenue
FROM trips
WHERE status = 'completed'
GROUP BY CONVERT(CHAR(7), trip_date, 120)
ORDER BY Month;

-- Q8. Revenue by Vehicle type

SELECT 
	vehicle_type, COUNT(status) AS NUmber_of_trips,
	   SUM(fare) AS Revenue
FROM trips t
   JOIN drivers d 
   ON t.driver_id = d.driver_id
WHERE status = 'completed'
GROUP BY vehicle_type;

-- BOUNS QUESTIONS
-- Q9 Which customer have never booked a trips

SELECT 
	c.customer_name AS customer_who_never_booked
FROM customers c
LEFT JOIN trips t
  ON c.customer_id = t.customer_id
WHERE t.customer_id IS NULL;

-- Q10. who are top 3 customer by total money spent

SELECT 
	TOP 3 c.customer_name AS customer, SUM(t.fare) AS Total_Money_Spent,
	   COUNT(status) AS Number_of_trips
FROM customers c
 JOIN trips t  ON
  c.customer_id = t.customer_id
WHERE t.status = 'completed'
GROUP BY c.customer_name
ORDER BY Total_Money_Spent DESC;