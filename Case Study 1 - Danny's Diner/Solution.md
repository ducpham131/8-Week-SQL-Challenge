# Solution
## Table of Content
[Common Table Expression](#common-table-expression)

[What is the total amount each customer spent at the restaurant?](#what-is-the-total-amount-each-customer-spent-at-the-restaurant?)

How many days has each customer visited the restaurant?
What was the first item from the menu purchased by each customer?
What is the most purchased item on the menu and how many times was it purchased by all customers?
Which item was the most popular for each customer?
Which item was purchased first by the customer after they became a member?
Which item was purchased just before the customer became a member?
What is the total items and amount spent for each member before they became a member?
If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
## Common Table Expression
Used **LEFT JOIN** to merge `sales`, `menu` and `member` tables. Calculated the difference between `order_date` and `join_date`, and names this calculated column `days_dif`.
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
## What is the total amount each customer spent at the restaurant?

