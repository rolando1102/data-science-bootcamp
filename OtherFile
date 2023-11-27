--1. How often, on average, do users create bookings?

SELECT 
 ROUND(COUNT(business_key)/COUNT(DISTINCT(user_key)),2) AS average_bookings
FROM 
  Booksy.Bookings;
/*2. Knowing that the 'bookings' table contains information about the creation date/time of bookings in UTC time, indicate when the most bookings are created in Chicago time with hourly intervals:
<0 - 6)
<6 - 12)
<12 - 18)
<18 - 0)*/

SELECT 
  COUNT(CASE WHEN EXTRACT(HOUR From created_utc) <6 THEN 1 END) AS intervals_0_6,
  COUNT(CASE WHEN EXTRACT(HOUR From created_utc) >= 6 AND EXTRACT(HOUR From created_utc) <12 THEN 1 END) AS intervals_6_12,
  COUNT(CASE WHEN EXTRACT(HOUR From created_utc) >= 12 AND EXTRACT(HOUR From created_utc) <18 THEN 1 END) AS intervals_12_18,
  COUNT(CASE WHEN EXTRACT(HOUR From created_utc) >= 18 THEN 1 END) AS intervals_18_0
FROM 
  Booksy.Bookings;

-- Most bookings is creating in Interval 12-18

/*3. Knowing that the 'businesses' table contains historical data about businesses, and 'business_id' remains constant over time for each business, indicate the number of businesses currently in the 'P' or 'S' status.*/

SELECT 
  status,
  Count(*) AS number_of_businesses
FROM  
  Booksy.Business
WHERE 
  effective_till IS NULL AND
  status IN ('P','S')
GROUP BY status;
-- Companies with status P = 39 and S = 789


/*4. What is the average price for a service for bookings in each month?*/


WITH data1 AS(
  SELECT
    t1.service_key,
    t1.created_utc,
    t2.service_variant_price AS service_price,
    t2.service_key AS service_key2,
    EXTRACT(YEAR FROM created_utc) AS year,
    EXTRACT(MONTH FROM created_utc) AS month,
  FROM Booksy.Bookings AS t1
  LEFT JOIN Booksy.Service AS t2
  ON t1.service_key = t2.service_key)
SELECT 
  data1.year,
  data1.month,
  ROUND(AVG(data1.service_price),2) AS avg_price
FROM data1
WHERE service_key2  IS NOT NULL
GROUP BY 1,2
ORDER BY 1 ASC,2 ASC;

/*5. On average, how many days after creating a user account does a user create their first booking?*/

WITH User_First_booking AS (
  SELECT
    t1.user_key,
    t1.created AS user_created_date,
    t2.created_utc AS date_booking,
    ROW_NUMBER() OVER (PARTITION BY t1.user_key ORDER BY t2.created_utc) AS booking_rank
  FROM Booksy.User AS t1
  LEFT JOIN Booksy.Bookings AS t2
  ON t1.user_key = t2.user_key
  ORDER BY user_key)
SELECT
  AVG(DATETIME_DIFF(DATETIME(date_booking),DATETIME(user_created_date),HOUR)/24) AS avg_first_booking
FROM User_First_booking
WHERE 
  booking_rank = 1 AND
  date_booking IS NOT NULL;


--AVG First_booking 420 days 


