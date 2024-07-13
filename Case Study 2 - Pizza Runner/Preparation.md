# Preparation
## :triangular_flag_on_post: Table of Content
**Cleaning Data**
- [`runner_order`](#runner_order)
- [`customer_order`](#customer_order)

**CTEs**
- [`cook`](#cook): Extract `pizza_receipt`
- [`order_table`](#order_table)
- [`prepare_order`](#prepare_order)
- [`pizza_recipe`](#pizza_recipe)
- [`delivered_order`](#delivered_order)
## Cleaning Data
### `runner_order`
```c
SELECT
	order_id,
	runner_id,
-- Replace blank and change datatype
-- pickup_time
	CAST(
     		CASE WHEN pickup_time IN ('','null')
      				THEN NULL
      			ELSE pickup_time  END
      	AS TIMESTAMP) AS pickup_time,
-- distance
	CAST(
     		CASE WHEN distance IN ('','null')
       				THEN NULL
      			WHEN distance LIKE '%km%'
       				THEN SUBSTRING(distance FROM 0 FOR POSITION('km' IN distance))
            	ELSE distance END
       	AS FLOAT) AS distance_km,
-- duration
	CAST(
      		CASE WHEN duration IN ('','null')
      				THEN NULL
      			WHEN duration LIKE '%min%'
      				THEN SUBSTRING(duration FROM 0 FOR POSITION('min' IN duration))
      			ELSE duration END
	AS FLOAT) AS duration_minute,
-- cancellation
	CASE WHEN cancellation IN ('','null')
			THEN NULL
         	ELSE cancellation END AS cancellation
FROM pizza_runner.runner_orders
```
Result:

| order_id | runner_id | pickup_time              | distance_km | duration_minute | cancellation            |
| -------- | --------- | ------------------------ | ----------- | --------------- | ----------------------- |
| 1        | 1         | 2020-01-01T18:15:34.000Z | 20          | 32              |                         |
| 2        | 1         | 2020-01-01T19:10:54.000Z | 20          | 27              |                         |
| 3        | 1         | 2020-01-03T00:12:37.000Z | 13.4        | 20              |                         |
| 4        | 2         | 2020-01-04T13:53:03.000Z | 23.4        | 40              |                         |
| 5        | 3         | 2020-01-08T21:10:57.000Z | 10          | 15              |                         |
| 6        | 3         |                          |             |                 | Restaurant Cancellation |
| 7        | 2         | 2020-01-08T21:30:45.000Z | 25          | 25              |                         |
| 8        | 2         | 2020-01-10T00:15:02.000Z | 23.4        | 15              |                         |
| 9        | 2         |                          |             |                 | Customer Cancellation   |
| 10       | 1         | 2020-01-11T18:50:20.000Z | 10          | 10              |                         |
### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
### customer_order 
```c
SELECT
	order_id,
	customer_id,
	pizza_id,
-- Replace "fake-null" by "real-null" :D
--- exclusions
	CASE WHEN exclusions IN ('','null')
			THEN NULL
		ELSE exclusions END AS exclusions,
--- extras
	CASE WHEN extras IN ('','null')
			THEN NULL
		ELSE extras END AS extras,
		order_time
FROM pizza_runner.customer_orders
```
Result:

| order_id | customer_id | pizza_id | exclusions | extras | order_time               |
| -------- | ----------- | -------- | ---------- | ------ | ------------------------ |
| 1        | 101         | 1        |            |        | 2020-01-01T18:05:02.000Z |
| 2        | 101         | 1        |            |        | 2020-01-01T19:00:52.000Z |
| 3        | 102         | 1        |            |        | 2020-01-02T23:51:23.000Z |
| 3        | 102         | 2        |            |        | 2020-01-02T23:51:23.000Z |
| 4        | 103         | 1        | 4          |        | 2020-01-04T13:23:46.000Z |
| 4        | 103         | 1        | 4          |        | 2020-01-04T13:23:46.000Z |
| 4        | 103         | 2        | 4          |        | 2020-01-04T13:23:46.000Z |
| 5        | 104         | 1        |            | 1      | 2020-01-08T21:00:29.000Z |
| 6        | 101         | 2        |            |        | 2020-01-08T21:03:13.000Z |
| 7        | 105         | 2        |            | 1      | 2020-01-08T21:20:29.000Z |
| 8        | 102         | 1        |            |        | 2020-01-09T23:54:33.000Z |
| 9        | 103         | 1        | 4          | 1, 5   | 2020-01-10T11:22:59.000Z |
| 10       | 104         | 1        |            |        | 2020-01-11T18:34:49.000Z |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2020-01-11T18:34:49.000Z |
### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## CTEs
### cook
```c
SELECT
	pizza_id,
	CAST(UNNEST(STRING_TO_ARRAY(toppings, ',')) AS INTEGER)  AS topping_id
FROM pizza_runner.pizza_recipes
```
Result:

| pizza_id | topping_id |
| -------- | ---------- |
| 1        | 1          |
| 1        | 2          |
| 1        | 3          |
| 1        | 4          |
| 1        | 5          |
| 1        | 6          |
| 1        | 8          |
| 1        | 10         |
| 2        | 4          |
| 2        | 6          |
| 2        | 7          |
| 2        | 9          |
| 2        | 11         |
| 2        | 12         |
### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
### order_table
```c
SELECT
	*
FROM runner_order
INNER JOIN customer_order
USING(order_id)
INNER JOIN pizza_runner.pizza_names
USING(pizza_id);
```
Result:

| pizza_id | order_id | runner_id | pickup_time              | distance_km | duration_minute | cancellation            | customer_id | exclusions | extras | order_time               | pizza_name |
| -------- | -------- | --------- | ------------------------ | ----------- | --------------- | ----------------------- | ----------- | ---------- | ------ | ------------------------ | ---------- |
| 1        | 10       | 1         | 2020-01-11T18:50:20.000Z | 10          | 10              |                         | 104         | 2, 6       | 1, 4   | 2020-01-11T18:34:49.000Z | Meatlovers |
| 1        | 2        | 1         | 2020-01-01T19:10:54.000Z | 20          | 27              |                         | 101         |            |        | 2020-01-01T19:00:52.000Z | Meatlovers |
| 1        | 3        | 1         | 2020-01-03T00:12:37.000Z | 13.4        | 20              |                         | 102         |            |        | 2020-01-02T23:51:23.000Z | Meatlovers |
| 1        | 8        | 2         | 2020-01-10T00:15:02.000Z | 23.4        | 15              |                         | 102         |            |        | 2020-01-09T23:54:33.000Z | Meatlovers |
| 1        | 9        | 2         |                          |             |                 | Customer Cancellation   | 103         | 4          | 1, 5   | 2020-01-10T11:22:59.000Z | Meatlovers |
| 1        | 10       | 1         | 2020-01-11T18:50:20.000Z | 10          | 10              |                         | 104         |            |        | 2020-01-11T18:34:49.000Z | Meatlovers |
| 1        | 1        | 1         | 2020-01-01T18:15:34.000Z | 20          | 32              |                         | 101         |            |        | 2020-01-01T18:05:02.000Z | Meatlovers |
| 1        | 4        | 2         | 2020-01-04T13:53:03.000Z | 23.4        | 40              |                         | 103         | 4          |        | 2020-01-04T13:23:46.000Z | Meatlovers |
| 1        | 4        | 2         | 2020-01-04T13:53:03.000Z | 23.4        | 40              |                         | 103         | 4          |        | 2020-01-04T13:23:46.000Z | Meatlovers |
| 1        | 5        | 3         | 2020-01-08T21:10:57.000Z | 10          | 15              |                         | 104         |            | 1      | 2020-01-08T21:00:29.000Z | Meatlovers |
| 2        | 3        | 1         | 2020-01-03T00:12:37.000Z | 13.4        | 20              |                         | 102         |            |        | 2020-01-02T23:51:23.000Z | Vegetarian |
| 2        | 7        | 2         | 2020-01-08T21:30:45.000Z | 25          | 25              |                         | 105         |            | 1      | 2020-01-08T21:20:29.000Z | Vegetarian |
| 2        | 6        | 3         |                          |             |                 | Restaurant Cancellation | 101         |            |        | 2020-01-08T21:03:13.000Z | Vegetarian |
| 2        | 4        | 2         | 2020-01-04T13:53:03.000Z | 23.4        | 40              |                         | 103         | 4          |        | 2020-01-04T13:23:46.000Z | Vegetarian |
### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
### prepare_order
```c
SELECT DISTINCT
	order_id,
	runner_id,
	order_time,
	pickup_time,
	EXTRACT(EPOCH FROM (pickup_time - order_time)) / 60 AS time_prepare
FROM order_table
WHERE pickup_time IS NOT NULL
ORDER BY order_id;
```
Result:
| order_id | runner_id | order_time               | pickup_time              | time_prepare       |
| -------- | --------- | ------------------------ | ------------------------ | ------------------ |
| 1        | 1         | 2020-01-01T18:05:02.000Z | 2020-01-01T18:15:34.000Z | 10.533333333333333 |
| 2        | 1         | 2020-01-01T19:00:52.000Z | 2020-01-01T19:10:54.000Z | 10.033333333333333 |
| 3        | 1         | 2020-01-02T23:51:23.000Z | 2020-01-03T00:12:37.000Z | 21.233333333333334 |
| 4        | 2         | 2020-01-04T13:23:46.000Z | 2020-01-04T13:53:03.000Z | 29.283333333333335 |
| 5        | 3         | 2020-01-08T21:00:29.000Z | 2020-01-08T21:10:57.000Z | 10.466666666666667 |
| 7        | 2         | 2020-01-08T21:20:29.000Z | 2020-01-08T21:30:45.000Z | 10.266666666666667 |
| 8        | 2         | 2020-01-09T23:54:33.000Z | 2020-01-10T00:15:02.000Z | 20.483333333333334 |
| 10       | 1         | 2020-01-11T18:34:49.000Z | 2020-01-11T18:50:20.000Z | 15.516666666666667 |
### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---

### pizza_recipe
```c
SELECT
pizza_id,
CAST(value AS INT) AS topping_id
FROM pizza_runner.pizza_recipes
CROSS JOIN
UNNEST(string_to_array(toppings, ',')::int[]) AS t(value);
```
Result:

| pizza_id | topping_id |
| -------- | ---------- |
| 1        | 1          |
| 1        | 2          |
| 1        | 3          |
| 1        | 4          |
| 1        | 5          |
| 1        | 6          |
| 1        | 8          |
| 1        | 10         |
| 2        | 4          |
| 2        | 6          |
| 2        | 7          |
| 2        | 9          |
| 2        | 11         |
| 2        | 12         |
### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
### delivered_order
```c
SELECT
		order_id,
		COUNT(pizza_id) AS ordered_quanity
FROM order_table
WHERE duration_minute IS NOT NULL
GROUP BY order_id;
```
Result:

| order_id | ordered_quanity |
| -------- | --------------- |
| 3        | 2               |
| 5        | 1               |
| 4        | 3               |
| 10       | 2               |
| 2        | 1               |
| 7        | 1               |
| 1        | 1               |
| 8        | 1               |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
