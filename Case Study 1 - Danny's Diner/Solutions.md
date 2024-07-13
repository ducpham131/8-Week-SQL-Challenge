# :bulb: Solution
## :triangular_flag_on_post: Table of Content
[Common Table Expression](#common-table-expression)

[1. What is the total amount each customer spent at the restaurant?](#1-what-is-the-total-amount-each-customer-spent-at-the-restaurant)

[2. How many days has each customer visited the restaurant?](#2-how-many-days-has-each-customer-visited-the-restaurant)

[3. What was the first item from the menu purchased by each customer?](#3-what-was-the-first-item-from-the-menu-purchased-by-each-customer)

[4. What is the most purchased item on the menu and how many times was it purchased by all customers?](#4-what-is-the-most-purchased-item-on-the-menu-and-how-many-times-was-it-purchased-by-all-customers)

[5. Which item was the most popular for each customer?](#5-which-item-was-the-most-popular-for-each-customer)

[6. Which item was purchased first by the customer after they became a member?](#6-which-item-was-purchased-first-by-the-customer-after-they-became-a-member)

[7. Which item was purchased just before the customer became a member?](#7-which-item-was-purchased-just-before-the-customer-became-a-member)

[8. What is the total items and amount spent for each member before they became a member?](#8-what-is-the-total-items-and-amount-spent-for-each-member-before-they-became-a-member)

[9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?](#9-if-each-1-spent-equates-to-10-points-and-sushi-has-a-2x-points-multiplier---how-many-points-would-each-customer-have)

[10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?](#10-in-the-first-week-after-a-customer-joins-the-program-including-their-join-date-they-earn-2x-points-on-all-items-not-just-sushi---how-many-points-do-customer-a-and-b-have-at-the-end-of-january)
## Common Table Expression
Use **LEFT JOIN** to merge `sales`, `menu` and `member` tables. Calculate the difference between `order_date` and `join_date`, and name this calculated column `days_dif`.
```c
WITH join_table AS(
SELECT
  *,
  order_date - join_date AS days_dif
FROM dannys_diner.sales
LEFT JOIN dannys_diner.menu
USING(product_id)
LEFT JOIN dannys_diner.members
USING(customer_id)
)
```
Result:
| customer_id | product_id | order_date               | product_name | price | join_date                | days_dif |
| ----------- | ---------- | ------------------------ | ------------ | ----- | ------------------------ | -------- |
| A           | 2          | 2021-01-07T00:00:00.000Z | curry        | 15    | 2021-01-07T00:00:00.000Z | 0        |
| A           | 3          | 2021-01-11T00:00:00.000Z | ramen        | 12    | 2021-01-07T00:00:00.000Z | 4        |
| A           | 3          | 2021-01-11T00:00:00.000Z | ramen        | 12    | 2021-01-07T00:00:00.000Z | 4        |
| A           | 3          | 2021-01-10T00:00:00.000Z | ramen        | 12    | 2021-01-07T00:00:00.000Z | 3        |
| A           | 1          | 2021-01-01T00:00:00.000Z | sushi        | 10    | 2021-01-07T00:00:00.000Z | -6       |
| A           | 2          | 2021-01-01T00:00:00.000Z | curry        | 15    | 2021-01-07T00:00:00.000Z | -6       |
| B           | 1          | 2021-01-04T00:00:00.000Z | sushi        | 10    | 2021-01-09T00:00:00.000Z | -5       |
| B           | 1          | 2021-01-11T00:00:00.000Z | sushi        | 10    | 2021-01-09T00:00:00.000Z | 2        |
| B           | 2          | 2021-01-01T00:00:00.000Z | curry        | 15    | 2021-01-09T00:00:00.000Z | -8       |
| B           | 2          | 2021-01-02T00:00:00.000Z | curry        | 15    | 2021-01-09T00:00:00.000Z | -7       |
| B           | 3          | 2021-01-16T00:00:00.000Z | ramen        | 12    | 2021-01-09T00:00:00.000Z | 7        |
| B           | 3          | 2021-02-01T00:00:00.000Z | ramen        | 12    | 2021-01-09T00:00:00.000Z | 23       |
| C           | 3          | 2021-01-01T00:00:00.000Z | ramen        | 12    |                          |          |
| C           | 3          | 2021-01-01T00:00:00.000Z | ramen        | 12    |                          |          |
| C           | 3          | 2021-01-07T00:00:00.000Z | ramen        | 12    |                          |          |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 1. What is the total amount each customer spent at the restaurant?
Group `join_table` by the `customer_id` column. Calculate the sum of the `price` column for each customer and name this sum `total_spent`.
```c
SELECT
	customer_id,
	SUM(price) AS total_spent
FROM join_table
GROUP BY customer_id
ORDER BY customer_id
;
```
Result:
| customer_id | total_spent |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 2. How many days has each customer visited the restaurant?
Group `join_table` by the `customer_id` column. Count the distinct number of `order_date` values for each customer and names this count `days_visit`.
```c
SELECT
	customer_id,
	COUNT(DISTINCT order_date) AS days_visit
FROM join_table
GROUP BY customer_id
ORDER BY customer_id
;
```
Result:

| customer_id | days_visit |
| ----------- | ---------- |
| A           | 4          |
| B           | 6          |
| C           | 2          |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 3. What was the first item from the menu purchased by each customer?
Create a Subquery `first_time` to find the first (earliest) order date for each customer. 

Join the original `join_table` with the `first_time` subquery on `customer_id` and `order_date` to match the first order date. Selects the `customer_id` and `product_name` for the matched records.

The result will be a list of customers and the product names of their first orders.
```c
SELECT
	join_table.customer_id,
	product_name
FROM join_table
INNER JOIN (
	SELECT
		customer_id,
		MIN(order_date) AS first_order
	FROM join_table
	GROUP BY customer_id) AS first_time
ON join_table.customer_id = first_time.customer_id
		AND join_table.order_date = first_time.first_order
;
```
Result:
| customer_id | product_name |
| ----------- | ------------ |
| A           | sushi        |
| A           | curry        |
| B           | curry        |
| C           | ramen        |
| C           | ramen        |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 4. What is the most purchased item on the menu and how many times was it purchased by all customers?
Create the subquery aggregates data by `product_name` and `order_date`. Sum the `quanity` and `order_times` from the subquery for each product_name.

Group the results by `product_name`. Then order the products by the total `quanity` in descending order. Limit the result to the top 1 product with the highest quantity ordered.

The result will be the product name with the highest quantity ordered, along with the total quantity ordered and the total times it was purchased by distinct customers.
```c
SELECT
	product_name,
	SUM(quanity) AS quanity,
	SUM(order_times) AS times_purchased
FROM (
	SELECT
		product_name,
		order_date,
		COUNT(product_name) AS quanity,
		COUNT(DISTINCT customer_id) AS order_times
	FROM join_table
	GROUP BY product_name, order_date
	) AS items
GROUP BY product_name
ORDER BY quanity DESC
LIMIT 1
;
```
Result:

| product_name | quanity | times_purchased |
| ------------ | ------- | --------------- |
| ramen        | 8       | 6               |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 5. Which item was the most popular for each customer?
Calculates the number of times each product was purchased by each customer (`times_purchase`) in subquery.

Ranks these products for each customer using the **DENSE_RANK** function based on the purchase count, with the most frequently purchased product getting rank **1**.

Selects the `customer_id` and `product_name` for products that have the highest purchase count (`order_rank` = 1) for each customer.

The result will be a list of customers and the names of the products they purchased most frequently. If a customer has multiple products with the same highest purchase count, all such products will be included.

```c
SELECT
	customer_id,
	product_name
FROM (
	SELECT
		customer_id,
		product_name,
		COUNT( order_date) AS times_purchase,
		DENSE_RANK() OVER ( PARTITION BY customer_id ORDER BY COUNT(order_date) DESC) AS order_rank
	FROM join_table
	GROUP BY customer_id, product_name
	) AS rank_item
WHERE order_rank = 1
;
```
Result:

| customer_id | product_name |
| ----------- | ------------ |
| A           | ramen        |
| B           | ramen        |
| B           | curry        |
| B           | sushi        |
| C           | ramen        |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 6. Which item was purchased first by the customer after they became a member?
Identify the minimum number of days difference (`days_dif`) between `order_date` and `join_date` for each customer where the `order_date` is on or after the `join_date` in the subquery.

Join the original `join_table` with the `first_order` subquery on `customer_id` and `days_dif`.

Select the `customer_id` and `product_name` for records where the `days_dif` matches the minimum days difference for each customer.

The result will be a list of customers and the names of the products they ordered on their first order date after joining, based on the minimum difference in days between the `order_date` and `join_date`.
```c
SELECT
	join_table.customer_id,
	product_name
FROM join_table
INNER JOIN (
	SELECT
		customer_id,
		MIN(days_dif)
	FROM join_table
	WHERE order_date >= join_date
	GROUP BY customer_id) AS first_order
ON join_table.customer_id = first_order.customer_id
AND join_table.days_dif = first_order.min
;
```
Result:

| customer_id | product_name |
| ----------- | ------------ |
| A           | curry        |
| B           | sushi        |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 7. Which item was purchased just before the customer became a member?
Identify the maximum number of days difference (`days_dif`) between `order_date` and `join_date` for each customer where the `order_date` is before the `join_date` in the subquery.

Join the original `join_table` with the `last_order` subquery on `customer_id` and `days_dif`.

Selects the `customer_id` and `product_name` for records where the `days_dif` matches the maximum days difference for each customer when the order date is before the join date.

The result will be a list of customers and the names of the products they ordered on the latest date before joining.

```c
SELECT
	join_table.customer_id,
	product_name
FROM join_table
INNER JOIN (
	SELECT
		customer_id,
		MAX(days_dif)
	FROM join_table
	WHERE order_date < join_date
	GROUP BY customer_id) AS last_order
ON join_table.customer_id = last_order.customer_id
AND join_table.days_dif = last_order.max
;
```
Result:

| customer_id | product_name |
| ----------- | ------------ |
| A           | sushi        |
| A           | curry        |
| B           | sushi        |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 8. What is the total items and amount spent for each member before they became a member?
Filter the rows where the `order_date` is less than the `join_date`. This condition ensures that only orders placed before the customer joined are considered.

Calculate the total number of items purchased (`total_item`) and the total amount spent (`total_spent`) for each customer (`customer_id`) based on orders placed (`order_date`) before they joined (`join_date`).
```c
SELECT
	customer_id,
	COUNT(product_name) AS total_item,
	SUM(price) AS total_spent
FROM join_table
WHERE order_date < join_date
GROUP BY customer_id
;
```
Result:

| customer_id | total_item | total_spent |
| ----------- | ---------- | ----------- |
| B           | 3          | 40          |
| A           | 2          | 25          |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
Group `join_table` by customer_id. Aggregate `total_point` for each unique `customer_id` with conditions:
- If product_name is 'sushi', calculates price * 10 * 2. Otherwise, calculates price * 10.
Sum all aggregate results as `total_point`.
```c
SELECT
	customer_id,
	SUM( CASE WHEN product_name = 'sushi' THEN price*10*2
					ELSE price*10 END) AS total_point
FROM join_table
GROUP BY customer_id
;
```
Result:

| customer_id | total_point |
| ----------- | ----------- |
| A           | 860         |
| B           | 940         |
| C           | 360         |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
## 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
Filter rows where the `order_date` month is January and `customer_id` is customer A and B.

Evaluates each row based on conditions:
- When days_dif >= 0 AND days_dif <= 7 then x2 point of all items.
- x2 point if product_name is 'sushi'.
- For all other cases, multiply `price` by 10.
```c
SELECT
	customer_id,
	SUM( CASE WHEN days_dif >= 0 AND days_dif <= 7
        		THEN price*10*2
        	WHEN product_name = 'sushi' 
        		THEN price*10*2
        	ELSE price*10
        	END) AS total_point
FROM join_table
WHERE EXTRACT(MONTH FROM order_date) = 1
	AND customer_id IN ('A','B')
GROUP BY customer_id
;
```
Result:

| customer_id | total_point |
| ----------- | ----------- |
| A           | 1370        |
| B           | 940         |

### :rewind: [*Back*](#triangular_flag_on_post-table-of-content) 
---
