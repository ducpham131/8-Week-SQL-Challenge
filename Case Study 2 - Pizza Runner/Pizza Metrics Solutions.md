# :bulb: Pizza Metrics
## :triangular_flag_on_post: Table of Content
[1. How many pizzas were ordered?](#1-how-many-pizzas-were-ordered)

[2. How many unique customer orders were made?](#2-how-many-unique-customer-orders-were-made)

[3. How many successful orders were delivered by each runner?](#3-how-many-successful-orders-were-delivered-by-each-runner)

[4. How many of each type of pizza was delivered?](#4-how-many-of-each-type-of-pizza-was-delivered)

[5. How many Vegetarian and Meatlovers were ordered by each customer?](#5-how-many-vegetarian-and-meatlovers-were-ordered-by-each-customer)

[6. What was the maximum number of pizzas delivered in a single order?](#6-what-was-the-maximum-number-of-pizzas-delivered-in-a-single-order)

[7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
](#7-for-each-customer-how-many-delivered-pizzas-had-at-least-1-change-and-how-many-had-no-changes)

[8. How many pizzas were delivered that had both exclusions and extras?](#8-how-many-pizzas-were-delivered-that-had-both-exclusions-and-extras)

[9. What was the total volume of pizzas ordered for each hour of the day?](#9-what-was-the-total-volume-of-pizzas-ordered-for-each-hour-of-the-day)

[10. What was the volume of orders for each day of the week?](#10-what-was-the-volume-of-orders-for-each-day-of-the-week)

## 1. How many pizzas were ordered?
```c
SELECT
	COUNT(pizza_id) AS total_ordered_pizza
FROM customer_order;
```
Result:
|total_ordered_pizza|
|:------------------|
|14|
### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 2. How many unique customer orders were made?
```c
SELECT
	customer_id,
	COUNT(DISTINCT order_id) AS orders
FROM customer_order
GROUP BY customer_id;
```
Result:
| customer_id | orders |
| ----------- | ------ |
| 101         | 3      |
| 102         | 2      |
| 103         | 2      |
| 104         | 2      |
| 105         | 1      |
### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 3. How many successful orders were delivered by each runner?
```c
SELECT
	runner_id,
	COUNT(order_id) AS success_order
FROM runner_order
WHERE duration_minute IS NOT NULL
GROUP BY runner_id
ORDER BY runner_id;
```
Result:
| runner_id | success_order |
| --------- | ------------- |
| 1         | 4             |
| 2         | 3             |
| 3         | 1             |
### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 4. How many of each type of pizza was delivered?
```c
SELECT
	pizza_name,
	COUNT(pizza_id) AS delivered_quanity
FROM runner_order
INNER JOIN customer_order
USING(order_id)
INNER JOIN pizza_runner.pizza_names
USING(pizza_id)
WHERE duration_minute IS NOT NULL
GROUP BY pizza_name;
```
Result:
| pizza_name | delivered_quanity |
| ---------- | ----------------- |
| Meatlovers | 9                 |
| Vegetarian | 3                 |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 5. How many Vegetarian and Meatlovers were ordered by each customer?
```c
SELECT
	customer_id,
	pizza_name,
	COUNT(pizza_id) AS ordered_quanity
FROM runner_order
INNER JOIN customer_order
USING(order_id)
INNER JOIN pizza_runner.pizza_names
USING(pizza_id)
GROUP BY customer_id, pizza_name
ORDER BY customer_id;
```
Result:

| customer_id | pizza_name | ordered_quanity |
| ----------- | ---------- | --------------- |
| 101         | Meatlovers | 2               |
| 101         | Vegetarian | 1               |
| 102         | Meatlovers | 2               |
| 102         | Vegetarian | 1               |
| 103         | Meatlovers | 3               |
| 103         | Vegetarian | 1               |
| 104         | Meatlovers | 3               |
| 105         | Vegetarian | 1               |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 6. What was the maximum number of pizzas delivered in a single order?
```c
SELECT
	MAX(ordered_quanity) AS max_number
FROM  delivered_order;
```
Result:

| max_number |
| ---------- |
| 3          |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
```c
SELECT
	customer_id,
	SUM( CASE WHEN exclusions IS NOT NULL OR extras IS NOT NULL
							THEN 1 ELSE 0 END) AS change,
	SUM( CASE WHEN exclusions IS NULL AND extras IS NULL
							THEN 1 ELSE 0 END) AS no_change
FROM order_table
WHERE duration_minute IS NOT NULL
GROUP BY customer_id
ORDER BY customer_id;
```
Result:

| customer_id | change | no_change |
| ----------- | ------ | --------- |
| 101         | 0      | 2         |
| 102         | 0      | 3         |
| 103         | 3      | 0         |
| 104         | 2      | 1         |
| 105         | 1      | 0         |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 8. How many pizzas were delivered that had both exclusions and extras?
``c
SELECT
	COUNT(pizza_id) AS exclusions_and_extras
FROM order_table
WHERE exclusions IS NOT NULL 
	AND extras IS NOT NULL;
 ```
Result:

| exclusions_and_extras |
| --------------------- |
| 2                     |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 9. What was the total volume of pizzas ordered for each hour of the day?
```c
SELECT
	EXTRACT( HOUR FROM order_time) AS hours,
	COUNT(pizza_id) AS volume
FROM order_table
GROUP BY EXTRACT( HOUR FROM order_time)
ORDER BY EXTRACT( HOUR FROM order_time);
```
Result:

| hours | volume |
| ----- | ------ |
| 11    | 1      |
| 13    | 3      |
| 18    | 3      |
| 19    | 1      |
| 21    | 3      |
| 23    | 3      |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 10. What was the volume of orders for each day of the week?
```c
SELECT
	TO_CHAR(order_time,'Day') AS days,
	COUNT(pizza_id) AS volume
FROM order_table
GROUP BY TO_CHAR(order_time,'Day')
ORDER BY COUNT(pizza_id) DESC;
```
Result:

| days      | volume |
| --------- | ------ |
| Saturday  | 5      |
| Wednesday | 5      |
| Thursday  | 3      |
| Friday    | 1      |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---

