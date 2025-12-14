
# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis  

## Short Description

The Retail Sales Analysis project focuses on analyzing transactional retail data using SQL to uncover sales trends, customer purchasing behavior, and product performance. The insights derived from this analysis help businesses improve decision-making related to sales strategy, inventory planning, and customer targeting.

## Objectives

1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Technical Tasks

* Designed and analyzed structured retail sales tables
* Wrote SQL queries using SELECT, WHERE, GROUP BY, HAVING, and ORDER BY
* Implemented JOINs (INNER, LEFT) to merge customer, product, and sales data
* Used aggregate functions such as SUM, COUNT, AVG, and MAX
* Performed time-based analysis (daily, monthly, yearly sales trends)
* Created subqueries and CTEs for advanced data analysis
* Ensured data accuracy through validation and handling null values

## Features / Highlights

* Revenue and quantity analysis by product category
* Identification of top-performing and low-performing products
* Customer segmentation based on purchase behavior
* Peak sales period and seasonal trend analysis
* Region-wise and store-wise performance insights
* Optimized SQL queries for faster execution



```sql
CREATE DATABASE p1_retail_db;

CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

### 2. Data Exploration & Cleaning

- **Record Count**: Determine the total number of records in the dataset.
- **Customer Count**: Find out how many unique customers are in the dataset.
- **Category Count**: Identify all unique product categories in the dataset.
- **Null Value Check**: Check for any null values in the dataset and delete records with missing data.

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;

SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Write a SQL query to retrieve all columns for sales made on '2022-11-05**:
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```
2. **Write Sale of all indivisual Category**:
```sql
SELECT category, 
SUM(total_sale) FROM retail_sales 
group by category;
```

3. **Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022**:
```sql
SELECT 
  *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND 
    TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND
    quantity >= 4
```
4. **Find average quantity sold per transaction for each product category**:
```sql
SELECT 
	category,
	AVG(quantity) as Average
FROM retail_sales 
group by category 
order by Average DESC;
```

5. **Write a SQL query to calculate the total sales (total_sale) for each category.**:
```sql
SELECT 
    category,
    SUM(total_sale) as net_sale,
    COUNT(*) as total_orders
FROM retail_sales
GROUP BY 1
```

6. **Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category**:
```sql
SELECT
    ROUND(AVG(age), 2) as avg_age
FROM retail_sales
WHERE category = 'Beauty';

```

7. **Determine total revenue generated per day.**:
```sql
SELECT sale_date, 
sum(total_sale) as Revenue 
FROM retail_sales 
GROUP BY sale_date
ORDER BY sale_date ;
```
8. **Write a SQL query to find all transactions where the total_sale is greater than 1000**:
```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000;
```
9. **Calculate profit for each transaction**:

```sql
SELECT 
transaction_id,(total_sale - cogs) as Profit 
FROM retail_sales 
order by Profit desc;

```

10. **Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.**:
```sql
SELECT 
    category,
    gender,
    COUNT(*) as total_trans
FROM retail_sales
GROUP 
    BY 
    category,
    gender
ORDER BY 1
```
11. **Identify repeat customers who have made more than 3 transactions**:

```sql
SELECT customer_id,
COUNT(transaction_id) AS Tranactions
from retail_sales 
group by customer_id
having COUNT(transaction_id)>3
order by Tranactions desc;

```

12. **Write a SQL query to calculate the average sale for each month. Find out best selling month in each year**:

```sql
SELECT 
       year,
       month,
    avg_sale
FROM 
(    
SELECT 
    EXTRACT(YEAR FROM sale_date) as year,
    EXTRACT(MONTH FROM sale_date) as month,
    AVG(total_sale) as avg_sale,
    RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) as rank
FROM retail_sales
GROUP BY 1, 2
) as t1
WHERE rank = 1

```
13. **Write a SQL query to find the top 5 customers based on the highest total sales**:

```sql
SELECT 
    customer_id,
    SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```


14. **Calculate gender-wise contribution to total sales (percentage) **:
```sql
SELECT gender, 
ROUND(
	(SUM(total_sale) * 100.0) / (SELECT SUM(total_sale) 
    FROM retail_sales),2 ) as Percentage 
FROM retail_sales 
group by gender;

```

15. **Write a SQL query to find the number of unique customers who purchased items from each category.**:
```sql
SELECT 
    category,    
    COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales
GROUP BY category
```

16. **Find the age group (e.g., 18–25, 26–35, 36–45, etc.) that generates the highest revenue**:

```sql
SELECT 
    age_group,
    total_revenue
FROM (
    SELECT
        CASE
            WHEN age BETWEEN 18 AND 25 THEN '18-25'
            WHEN age BETWEEN 26 AND 35 THEN '26-35'
            WHEN age BETWEEN 36 AND 45 THEN '36-45'
            ELSE '45+'
        END AS age_group,
        SUM(total_sale) AS total_revenue
    FROM retail_sales
    GROUP BY 
        CASE
            WHEN age BETWEEN 18 AND 25 THEN '18-25'
            WHEN age BETWEEN 26 AND 35 THEN '26-35'
            WHEN age BETWEEN 36 AND 45 THEN '36-45'
            ELSE '45+'
        END
) t
ORDER BY total_revenue DESC;

```

17. **Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)**:

```sql
WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift
```

18. **Determine the customer with the highest lifetime value**:

```sql
SELECT customer_id,
SUM(total_sale) AS Sale 
from retail_sales 
GROUP BY customer_id 
ORDER BY Sale desc limit 1 ;

```

19. **Find average total sale value per transaction**:

```sql

SELECT customer_id, 
AVG(total_sale) AS Average 
from retail_sales 
GROUP BY transaction_id 
ORDER BY Average ;

```

20. **Identify the top 5 highest-value transactions**:

```sql

SELECT *, SUM(total_sale) as Sale from retail_sales
GROUP BY transaction_id
ORDER BY Sale  desc limit 5 ;

```

## Reports

- **Sales Summary**: A detailed report summarizing total sales, customer demographics, and category performance.
- **Trend Analysis**: Insights into sales trends across different months and shifts.
- **Customer Insights**: Reports on top customers and unique customer counts per category.

## Conclusion

This project demonstrates strong proficiency in SQL for data analysis by transforming raw retail sales data into meaningful business insights. It highlights the ability to write optimized queries, analyze large datasets, and support strategic decisions related to sales growth, inventory management, and customer engagement.

