## Overview
This document contains SQL queries and analysis for Pizza Runner's pizza ordering metrics. The analysis focuses on understanding pizza orders, customer behavior, and delivery patterns.

## A. Pizza Metrics Questions & Answers

### 1. How many pizzas were ordered?
```sql
SELECT COUNT(order_id) AS total_no_of_pizzas_ordered
FROM customer_orders;
```
**Purpose**: Gets the total count of all pizza orders regardless of delivery status.

### 2. How many unique customer orders were made?
```sql
SELECT COUNT(DISTINCT (customer_id, order_id)) AS unique_orders
FROM customer_orders;
```
**Purpose**: Counts unique combinations of customer and order to determine how many distinct orders were placed.

### 3. How many successful orders were delivered by each runner?
```sql
SELECT runner_id, COUNT(order_id) AS total_successful_orders
FROM runner_orders
WHERE cancellation IS NULL
GROUP BY runner_id;
```
**Purpose**: Shows delivery performance by counting non-cancelled orders per runner.

### 4. How many of each type of pizza was delivered?
```sql
SELECT pizzaname, COUNT(*) AS delivered_count
FROM (
    SELECT co.order_id AS orderid, 
           pn.pizza_id AS pizzaid, 
           pn.pizza_name AS pizzaname
    FROM runner_orders AS ro
    JOIN customer_orders AS co ON ro.order_id = co.order_id
    JOIN pizza_names AS pn ON co.pizza_id = pn.pizza_id
    WHERE cancellation IS NULL
) AS delivered_pizzas
GROUP BY pizzaname;
```
**Purpose**: Analyzes pizza type popularity by counting delivered pizzas only (excludes cancelled orders).

### 5. How many Vegetarian and Meatlovers were ordered by each customer?
```sql
SELECT customer_id, pizzaname, COUNT(*) AS order_count
FROM (
    SELECT co.customer_id,
           co.order_id AS orderid, 
           pn.pizza_id AS pizzaid, 
           pn.pizza_name AS pizzaname
    FROM customer_orders AS co
    JOIN pizza_names AS pn ON co.pizza_id = pn.pizza_id
) AS all_orders
GROUP BY customer_id, pizzaname
ORDER BY customer_id;
```
**Purpose**: Shows customer preferences by counting total orders (including cancelled) for each pizza type per customer.

### 6. What was the maximum number of pizzas delivered in a single order?
```sql
SELECT order_id, COUNT(*) AS number_of_pizzas_in_single_order
FROM customer_orders
GROUP BY order_id
ORDER BY number_of_pizzas_in_single_order DESC
LIMIT 1;
```
**Purpose**: Identifies the largest single order to understand order size patterns.

### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
```sql
WITH row_definition AS (
    SELECT 
        co.customer_id, 
        co.order_id AS orderid, 
        pn.pizza_id AS pizzaid, 
        pn.pizza_name AS pizza_name,
        CASE WHEN exclusions IS NULL THEN 'no_change' ELSE 'change' END AS excl_ch,
        CASE WHEN extras IS NULL THEN 'no_change' ELSE 'change' END AS ext_ch
    FROM runner_orders AS ro
    JOIN customer_orders AS co ON ro.order_id = co.order_id
    JOIN pizza_names AS pn ON co.pizza_id = pn.pizza_id
    WHERE cancellation IS NULL
)
SELECT 
    customer_id,
    COUNT(CASE 
            WHEN excl_ch = 'no_change' AND ext_ch = 'no_change' 
            THEN 1 
         END) AS pizzas_with_no_changes,
    COUNT(CASE 
            WHEN excl_ch = 'change' OR ext_ch = 'change' 
            THEN 1 
         END) AS pizzas_with_changes
FROM row_definition
GROUP BY customer_id
ORDER BY customer_id;
```
**Purpose**: Analyzes customization patterns by customer, showing who prefers standard vs. modified pizzas.

### 8. How many pizzas were delivered that had both exclusions and extras?
```sql
WITH row_definition AS (
    SELECT 
        co.customer_id, 
        co.order_id AS orderid, 
        pn.pizza_id AS pizzaid, 
        pn.pizza_name AS pizza_name,
        CASE WHEN exclusions IS NULL THEN 'no_change' ELSE 'change' END AS excl_ch,
        CASE WHEN extras IS NULL THEN 'no_change' ELSE 'change' END AS ext_ch
    FROM runner_orders AS ro
    JOIN customer_orders AS co ON ro.order_id = co.order_id
    JOIN pizza_names AS pn ON co.pizza_id = pn.pizza_id
    WHERE cancellation IS NULL
)
SELECT 
    COUNT(CASE 
            WHEN excl_ch = 'change' AND ext_ch = 'change' 
            THEN 1 
         END) AS pizzas_with_exclusions_extras
FROM row_definition;
```
**Purpose**: Identifies heavily customized orders where customers both removed and added ingredients.

### 9. What was the total volume of pizzas ordered for each hour of the day?
```sql
SELECT
    EXTRACT(HOUR FROM order_time) AS order_hour,
    COUNT(*) AS total_orders
FROM customer_orders
GROUP BY order_hour 
ORDER BY order_hour ASC;
```
**Purpose**: Reveals peak ordering times to help with staffing and inventory planning.

### 10. What was the volume of orders for each day of the week?
```sql
SELECT
    TO_CHAR(order_time, 'Day') AS day_of_week,
    COUNT(*) AS total_orders
FROM customer_orders
GROUP BY day_of_week
ORDER BY MIN(order_time);
```
**Purpose**: Shows weekly ordering patterns to identify busy days and plan accordingly.

## Key Insights

- **Data Quality**: Proper cleaning of empty strings and null values is crucial for accurate analysis
- **Delivery Success**: Only queries 3, 4, 7, and 8 filter for successful deliveries (WHERE cancellation IS NULL)
- **Customer Behavior**: Queries 5, 7, and 8 help understand customer preferences and customization patterns
- **Operational Planning**: Queries 6, 9, and 10 provide insights for capacity planning and resource allocation

## Notes

- The analysis uses joins between `customer_orders`, `runner_orders`, and `pizza_names` tables
- CTEs (Common Table Expressions) are used for complex logic in queries 7 and 8
- Date/time functions are used for temporal analysis in queries 9 and 10
- Conditional aggregation with CASE statements helps categorize and count different scenarios
