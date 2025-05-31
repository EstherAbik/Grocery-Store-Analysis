# Grocery-Store-Sales-Analysis

## Project Overview
This project aims to provide the business insights of the E-Commerce performance from 2021–2022 for a grocery store
 
# Table of Content
- [Project Steps](#project-steps)
- [SQL Queries](#sql-queries)
## Tools
- SQL - design,implement and analyze [Download here](https://microsoft.com)
- Power BI
 
## SQL
## Project Steps:
### 1. Database Design:
- Identified the entities/tables and attributes/columns relevant to the fashion store (products, customers, orders, shippers, order details, categories, employee, suppliers)
- Designed an appropriate database schema using a relational model, considering
relationships, primary and foreign keys.

### 2. Data Collection and Loading:
- Loaded the data into the database using a suitable database management system


### 3. Data Analysis: 
- Explore the data to gain insights into the store's operations. Consider
performing queries, aggregations, and statistical analysis to uncover trends,
patterns, and key metrics.
- Analyze sales data to identify popular products, customer purchasing
behaviors, seasonal trends, etc.

### SQL Queries
```SQL
-- 1
CREATE VIEW product_orders AS 
SELECT products.productName, products.productID, order_details.orderID, customerID
FROM orders
CROSS JOIN order_details ON orders.orderID = order_details.orderID
CROSS JOIN products ON order_details.productID = products.productID;


SELECT customerName, orderID
FROM Customer as c
LEFT JOIN orders as o on c.customerID = o.customerID;

-- OR

-- 1
SELECT customerName, productName
FROM customer as c
LEFT JOIN product_order as p ON c.customerID = p.customerID; 


-- 2
SELECT orderID, employee.firstName, employee.lastName, concat(employee.firstName, " ", employee.lastName) AS FullName
FROM orders
JOIN employee ON orders.employeeID = employee.employeeID;

-- 3
SELECT customerID
FROM customer
WHERE customerID NOT IN(SELECT customerID FROM orders);

-- 4 
SELECT orderID, productName
FROM product_order;

-- 5
SELECT productID
FROM products
WHERE productID NOT IN (SELECT productID FROM order_details);

-- 6
SELECT customer.customerName, productName
FROM customer
CROSS JOIN product_orders ON customer.customerID = product_orders.customerID; 

-- 7
SELECT productName, categories.CategoryName
FROM products
LEFT JOIN categories ON products.categoryID = categories.CategoryID;

-- 8
SELECT orders.*, shippers.shipperName
FROM orders
LEFT JOIN shippers ON orders.shipperID = shippers.shipperID;

-- 9
SELECT customerName, orders.* 
FROM customer
JOIN orders ON customer.customerID = orders.customerID
WHERE customer.customerID > 50;

-- 10
SELECT employee.firstName, orders.orderID
FROM orders
JOIN employee ON employee.employeeID = orders.employeeID
WHERE orderID > 10400;


-- 11
SELECT productID, productName, price
FROM products
ORDER BY price DESC
LIMIT 1;


-- 12
SELECT productID, productName, price
FROM products
ORDER BY price DESC
LIMIT 1
OFFSET 1;


-- 13
SELECT productName, price
FROM products
ORDER BY price DESC;


-- 14
SELECT productID, productName, price
FROM products
ORDER BY price DESC
LIMIT 5;

-- 15
SELECT productID, productName, price
FROM products
ORDER BY price DESC
LIMIT 4
OFFSET 1;

-- 16
SELECT productName
FROM (
SELECT productName , ROW_NUMBER () OVER (ORDER BY price ASC) AS row_num
FROM products) AS p
WHERE row_num <= 1; 

SELECT ROW_NUMBER () OVER (ORDER BY price ASC) AS row_num, productName 
FROM products;
-- 17
SELECT productName
FROM products
WHERE price=(SELECT min(price) FROM products);
-

-- 18
 SELECT count(lastName)
 FROM employee
 WHERE lastName like "%D%";
 
-- 19
SELECT customerName, count(orderID)
FROM customer
JOIN orders ON customer.customerID = orders.customerID
GROUP BY customerName
ORDER BY count(orderId) DESC;

-- 20
SELECT sum(price)
FROM products;

-- 21
SELECT orderID, sum(price)
FROM order_details
JOIN products as p ON order_details.productID = p.productID
GROUP BY orderID
ORDER BY sum(price);


-- 22
SELECT CustomerID, round(sum(price))
FROM product_order AS po
JOIN products ON po.productID = products.productID
GROUP BY customerID
ORDER BY sum(price) DESC
LIMIT 1;


-- 23
SELECT customer.CustomerID, round(sum(price))
FROM product_order AS po
JOIN products ON po.productID = products.productID
JOIN customer ON po.customerID = customer.customerID
WHERE country = "canada"
GROUP BY customerID
ORDER BY sum(price) DESC;


-- 24
SELECT CustomerID, round(sum(price))
FROM product_order AS po
JOIN products ON po.productID = products.productID
GROUP BY customerID
ORDER BY sum(price) DESC
LIMIT 1
OFFSET 1;

SELECT shipperName, o.orderID, sum(price)
FROM shippers
JOIN orders AS o ON shippers.shipperID = o. shipperID
JOIN order_details AS od ON od.orderID = o.orderID
JOIN products AS p ON p.productID = od.productID
GROUP BY shipperName, orderID;

-- 25
SELECT shipperName, sum(price)
FROM shippers
JOIN orders AS o ON shippers.shipperID = o. shipperID
JOIN order_details AS od ON od.orderID = o.orderID
JOIN products AS p ON p.productID = od.productID
GROUP BY shipperName ;

-- 1
SELECT count(productID) AS 'Products sold'
FROM products;
-- 2
SELECT SUM(products.price * order_details.quantity) AS 'Total Revenue'
FROM orders
JOIN order_details ON order_details.orderID = orders.orderID
JOIN products ON products.productID = order_details.productID;

-- 3
SELECT CategoryName, count(DISTINCT(productName)) AS 'Count'
FROM products
JOIN categories ON products.categoryID = categories.CategoryID
GROUP BY CategoryName;

-- 4A
SELECT customerName, count(orderID) AS 'Number of Purchase transactions from each Customer'
FROM customer
JOIN orders ON orders.customerID = customer.customerID
GROUP BY customerName;

-- 4B
SELECT count(orderID) AS 'Number of Purchase transactions'
FROM orders;

-- 5
SELECT YEAR(orderDate), count(orderID) AS 'Count'
FROM orders
WHERE YEAR(orderDate) = 2022 OR YEAR(orderDate) = 2021
GROUP BY YEAR(orderDate) 
;


-- 6
SELECT count(customerID)
from customer;

-- 6A
SELECT count(DISTINCT(customerID)) AS 'Active Customers'
FROM orders;


-- 6B
SELECT count(customerID) AS ' Passive Customers'
FROM customer
WHERE customerID NOT IN (SELECT customerID FROM orders);

-- 7
 SELECT customerName, SUM(products.price * order_details.quantity) AS 'Purchase Price'
 FROM customer
 JOIN orders ON customer.customerID = orders.customerID
 JOIN order_details ON order_details.orderID = orders.orderID
 JOIN products ON order_details.productID = products.productID
 GROUP BY customerName
 ORDER BY sum(products.price * order_details.quantity) DESC
 LIMIT 5; 

-- 8
SELECT count(productName), productName
FROM product_orders
GROUP BY productName
ORDER BY count(productName) DESC
LIMIT 5;

-- 9
SELECT 
    SUM(products.price * order_details.quantity) AS total_revenue,
    CONCAT(MONTH(orderDate), ' ', YEAR(orderDate)) AS order_month_year
FROM orders
JOIN order_details ON order_details.orderID = orders.orderID
JOIN products ON products.productID = order_details.productID
GROUP BY order_month_year;

-- 10 
SELECT CategoryName, count(orders.orderID)
FROM categories
JOIN  products ON products.categoryID = categories.CategoryID
JOIN order_details ON order_details.productID = products.productID
JOIN orders ON orders.orderID = order_details.orderID
group by CategoryName
order by count(orders.orderID) DESC
LIMIT 1;
;


-- 11
SELECT customerName , orders.customerID, count(orders.customerID) AS 'Order Count'
FROM orders
JOIN customer ON customer.customerID = orders.customerID
GROUP BY orders.customerID
HAVING count(orders.customerID) > 2
order by count(orders.customerID) DESC ;

SELECT CategoryName, sum(price)
FROM categories
JOIN products ON categories.CategoryID = products.categoryID
GROUP BY CategoryName;


-- 12
SELECT 
	SUM(products.price * order_details.quantity) AS total_revenue, 
    employee.employeeID, 
    lastName
FROM employee
JOIN orders ON orders.employeeID = employee.employeeID
JOIN order_details ON orders.orderID = order_details.orderID
JOIN products ON order_details.productID = products.productID
GROUP BY employee.employeeID
ORDER BY sum(price) DESC
LIMIT 1;

-- 13
SELECT shipperName, count(orderID) AS 'Shipping Count'
FROM shippers
JOIN orders ON shippers.shipperID = orders.shipperID
GROUP BY shipperName
ORDER BY count(orderID) DESC
LIMIT 1;

-- 14
SELECT  supplierName, count(DISTINCT(orders.orderID)) AS 'Supplier Count'
FROM suppliers AS s
JOIN products AS p ON p.supplierID = s.supplierID  
JOIN order_details ON order_details.productID  = p.productID
JOIN orders ON orders.orderID = order_details.orderID
GROUP BY supplierName
ORDER BY count(orders.orderID) DESC 
LIMIT 2
 ;
```
Result
design and implement a database for an imaginary grocery store. The database should capture and organize various aspects of the store's operations, such as product inventory, customer information, orders, and sales data. Additionally, you will analyze the data and provide insights to support business decision-making.
Goals

The insight into the E-Commerce performance is shown in the dashboard report (USING POWER BI).


## POWER BI
## Project Steps
PowerBI was used for the visualization section after each report has been exported
