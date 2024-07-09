# Solution
## Table of Content
[Common Table Expression]
## Common Table Expression
Joined all table
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
