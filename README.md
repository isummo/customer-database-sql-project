-- Create Database and Use It
CREATE DATABASE customer_database;
USE customer_database;

-- Create Table
CREATE TABLE customer_info (
    cust_id INT,
    Name VARCHAR(50),
    city VARCHAR(50),
    Age INT,
    salary NUMERIC
);

-- Insert Values into Table
INSERT INTO customer_info (cust_id, Name, city, Age, salary)
VALUES
    (23451, 'Rishika Sharma', 'Ranchi', 21, 30900.90),
    (04572, 'Vanshila Raj', 'Pehalgam', 24, 49872.10),
    (2327,  'Gomti Bhatnagar', 'Bandra', 32, 342887.32),
    (6269,  'Keshev Kumar', 'Karachi', 31, 134002.23),
    (2269,  'Tannu Viswa', 'Panji', 29, 98370.35),
    (1242,  'Priya Rathore', 'Bohpal', 30, 88200.87),
    (13431, 'Bhusan Singh', 'Patna', 35, 81280.93);

-- Select All Records
SELECT * FROM customer_info;

-- Show Only Name and City
SELECT name, city FROM customer_info;

-- People Older Than 30
SELECT name AS 'customer_Age>30' FROM customer_info WHERE age > 30;

-- Customers from Delhi or Patna
SELECT name FROM customer_info WHERE city = 'delhi' OR city = 'patna';

-- Customers Under 30 in Ranchi
SELECT name AS 'Age under 30' FROM customer_info WHERE age < 30 AND city = 'Ranchi';

-- Sort by Salary (High to Low)
SELECT name, salary FROM customer_info ORDER BY salary DESC;

-- Sort by Name Alphabetically
SELECT * FROM customer_info ORDER BY name;

-- Who Earns Exactly 88200.87
SELECT * FROM customer_info WHERE salary = 88200.87;

-- PHASE 2: Aggregations

-- Average Age
SELECT AVG(age) FROM customer_info;

-- Total Salary Payout
SELECT SUM(salary) FROM customer_info;

-- Highest Salary
SELECT MAX(salary) FROM customer_info;

-- Lowest Age (Fixing incorrect label)
SELECT MIN(age) FROM customer_info;

-- Count of Customers per City
SELECT city, COUNT(name) AS cust_name FROM customer_info GROUP BY city;

-- PHASE 3: Real-Life Scenarios

-- Customers Earning Above Average Salary
SELECT * FROM customer_info WHERE salary > (SELECT AVG(salary) FROM customer_info);

-- Customers With Salary Ending in .90
SELECT * FROM customer_info WHERE salary LIKE '%.90';
-- or using numeric calculation
SELECT * FROM customer_info WHERE ROUND(salary - FLOOR(salary), 2) = .90;

-- PHASE 4: Data Update Practice

-- Update City Name 'Bohpal' → 'Bhopa'
UPDATE customer_info SET city = 'Bhopa' WHERE cust_id = 1242;

-- Increase Salary of All Customers by 10%
UPDATE customer_info SET salary = salary * 1.10;

-- Customers Whose Name Starts with 'P'
SELECT * FROM customer_info WHERE name LIKE 'P%';

-- Customers from Cities Not Starting with 'B'
SELECT * FROM customer_info WHERE city NOT LIKE 'B%';

-- Customers Aged 28–35 and Salary > 80K
SELECT * FROM customer_info WHERE age BETWEEN 28 AND 35 AND salary > 80000;

-- Customers Whose Name Contains 'sh'
SELECT name FROM customer_info WHERE name LIKE '%sh%';

-- Customers Not from Patna and Salary Under 1L
SELECT name, city, salary FROM customer_info WHERE city <> 'Patna' AND salary < 100000;

-- Subqueries

-- Customers With Salary Above Average
SELECT * FROM customer_info WHERE salary > (SELECT AVG(salary) FROM customer_info);

-- Customer(s) With Highest Salary
SELECT * FROM customer_info WHERE salary = (SELECT MAX(salary) FROM customer_info);

-- Customers from Cities with More Than 1 Customer
SELECT name FROM customer_info 
WHERE city IN (
    SELECT city FROM customer_info 
    GROUP BY city 
    HAVING COUNT(name) > 1
);

-- Sorting + Ranking

-- Top 3 Highest Paid Customers
SELECT * FROM customer_info ORDER BY salary DESC LIMIT 3;

-- Youngest 2 Customers
SELECT * FROM customer_info ORDER BY age ASC LIMIT 2;

-- Cities with Highest Average Salary
SELECT city, AVG(salary) AS AVG_salary FROM customer_info 
GROUP BY city 
ORDER BY AVG_salary DESC;

-- Analytical Thinking

-- Percentage of Customers Under 30
SELECT COUNT(*) * 100.0 / (SELECT COUNT(*) FROM customer_info) AS percent_under_30
FROM customer_info WHERE age < 30;

-- Average Salary by Age Group
SELECT 
    CASE
        WHEN age BETWEEN 20 AND 25 THEN '20-25'
        WHEN age BETWEEN 26 AND 30 THEN '26–30'
        WHEN age BETWEEN 31 AND 35 THEN '31–35'
        ELSE 'Others'
    END AS age_group,
    AVG(salary) AS AVG_salary
FROM customer_info
GROUP BY age_group;

-- or using UNION
SELECT '26–30' AS age_group , AVG(salary) AS AVG_salary FROM customer_info WHERE age BETWEEN 26 AND 30
UNION ALL 
SELECT '31–35' AS age_group , AVG(salary) AS AVG_salary FROM customer_info WHERE age BETWEEN 31 AND 35
UNION ALL 
SELECT '20–25' AS age_group , AVG(salary) AS AVG_salary FROM customer_info WHERE age BETWEEN 20 AND 25;

-- Cities with Average Salary > 90K
SELECT city, AVG(salary) AS AVG_salary FROM customer_info 
GROUP BY city 
HAVING AVG_salary > 90000;

-- Window Functions

-- Rank Customers by Salary
SELECT name, salary, RANK() OVER (ORDER BY salary DESC) AS salary_rank 
FROM customer_info;

-- Compare Each Customer’s Salary to Average of Their City (Advanced)
SELECT 
    name,
    city,
    salary,
    AVG(salary) OVER (PARTITION BY city) AS avg_city_salary,
    salary - AVG(salary) OVER (PARTITION BY city) AS diff_from_avg
FROM customer_info;
