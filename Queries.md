-- EXPLORATORY DATA ANALYSIS
-- Open the Database
USE SALES 
SELECT * 
from 
	sales
    
-- 1. Which table will help you analyze revenue information? sales data
SELECT 
	YEAR(`Order Date`) AS Year
FROM 
	sales;
    
-- 2. How many sales has the company made so far? 15,000
SELECT count(DISTINCT `Order ID`) as `Total Sales`
FROM sales;

-- 3. Which product had the highest quantity sold? Proseware High-Performance Business-Class Laser Fax X200 Black with 1560 qty at N1496.89
SELECT 
	b.ProductKey as ID, b.ProductName, ROUND(MAX(a.`Order Qty`),2) as `Highest Qty`, `Cost of Sales`
FROM sales a
INNER JOIN salesproduct b
ON a.ProductKey = b.ProductKey;

-- 4. I want to see the order date, order quantity, and the channels through wich these orders were made.
-- LEFT JOIN
SELECT
	a.ChannelName, b.`Order Date`, b.`Order Qty`, b.`Order ID` from sales b
LEFT JOIN
	saleschannel a 
on 
	a.ChannelKey = b.ChanelKey;
    
-- 5. What is the date range in which all orders were made? Between 1/1/2012 and 9/9/2014
SELECT
	MIN(`Order Date`) as `First Date`,
    MAX(`Order Date`) as `Last Date`
FROM 
	sales;
Select * from sales

-- 6. Pull a count of orders sliced by product_id for the year 2014
-- 6a. Compare that to a count of orders from 2013 and calculate the YoY increase;
CREATE TEMPORARY TABLE tempTable
SELECT
	salesproduct.ProductKey,
    salesproduct.ProductName,
    year(sales.`Order Date`) as Year,
    count(sales.`Order date`) as Orders,
    count(case when year(sales.`Order Date`) like 2014 then sales.`Order ID` else null end) as 2014_Orders,
    count(case when year(sales.`Order Date`) like 2013 then sales.`Order ID` else null end) as 2013_Orders
FROM
	sales 
LEFT JOIN salesproduct
ON	sales.ProductKey = salesproduct.Productkey
WHERE
	YEAR(sales.`Order Date`) in (2014,2013)
GROUP BY salesproduct.ProductKey, salesproduct.ProductName
ORDER BY salesproduct.ProductKey;

SELECT
	ProductKey, ProductName,
	2014_Orders,
	2013_Orders,
round((2014_Orders - 2013_Orders)/2013_Orders*100) AS 'YoYGrowth%'
 FROM
	tempTable;
    
select * from sales
-- 7. What date did we make the highest sale?  15th April, 2014
SELECT
	`Order Date`,
	COUNT(`Order ID`) as No_of_Orders,
	ROUND((Price * `Order Qty`),2) as Revenue,
	ROUND((`Unit Cost` * `Order Qty`),2) as Cost_Of_Production,
	ROUND(((Price * `Order Qty`)-(`Unit Cost` * `Order Qty`)),2) as Profit
FROM sales
GROUP BY `Order Date`   
ORDER BY Profit 
DESC;