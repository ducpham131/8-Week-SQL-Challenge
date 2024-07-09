# Solution
## Table of Content
[Common Table Expression](#common-table-expression)

[1. What is the total amount each customer spent at the restaurant?](#1-What-is-the-total-amount-each-customer-spent-at-the-restaurant)

[2. How many days has each customer visited the restaurant?](#2-How-many-days-has-each-customer-visited-the-restaurant?)

[3. What was the first item from the menu purchased by each customer?](#3-What-was-the-first-item-from-the-menu-purchased-by-each-customer?)

What is the most purchased item on the menu and how many times was it purchased by all customers?
Which item was the most popular for each customer?
Which item was purchased first by the customer after they became a member?
Which item was purchased just before the customer became a member?
What is the total items and amount spent for each member before they became a member?
If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
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

---
