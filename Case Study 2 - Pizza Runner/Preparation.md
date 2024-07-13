# :bulb: CTEs
## :triangular_flag_on_post: Table of Content
[`runner_order`](#runner_order)

[`customer_order`](#customer_order)
## `runner_order`
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
---
## customer_order 
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

---

