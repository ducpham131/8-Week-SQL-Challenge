# Case Study 1: Danny's Dinner
<img src="https://8weeksqlchallenge.com/images/case-study-designs/1.png" width="500">

## Introduction
Danny, owner of a Japanese restaurant serving sushi, curry, and ramen, wants to use his data to answer some questions about his customers. Having a deeper connection with his customers will help him deliver a better and more personalized experience for loyal patrons. He's been collecting customer data for a few months, but lacks the insights to improve business. Let's help Danny turn his passion into a thriving restaurant!

*Source - https://8weeksqlchallenge.com/case-study-1/*

## Available Data
There are 3 key datasets for this case study:

- [sales](#table-1-sales)
- [menu](#table-2-menu)
- [members](#table-3-members)
### Table 1: sales
The `sales` table captures all `customer_id` level purchases with an corresponding `order_date` and `product_id` information for when and what menu items were ordered.

|customer_id|order_date|product_id|
|:---------:|:--------:|:--------:|
|A|2021-01-01|1|
|A|2021-01-01|2|
|A|2021-01-07|2|
|A|2021-01-10|3|
|A|2021-01-11|3|
|A|2021-01-11|3|
|B|2021-01-01|2|
|B|2021-01-02|2|
|B|2021-01-04|1|
|B|2021-01-11|1|
|B|2021-01-16|3|
|B|2021-02-01|3|
|C|2021-01-01|3|
|C|2021-01-01|3|
|C|2021-01-07|3|

### Table 2: menu
The `menu` table maps the `product_id` to the actual `product_name` and `price` of each menu item.

|product_id|product_name|price|
|:--------:|:----------:|:---:|
|1|sushi|10|
|2|curry|15|
|3|ramen|12|

### Table 3: members
The final `members` table captures the `join_date` when a `customer_id` joined the beta version of the Danny’s Diner loyalty program.

|customer_id|join_date|
|:---------:|:-------:|
|A|2021-01-07|
|B|2021-01-09|

## Entity Relationship Diagram

<img src=https://github.com/ducpham131/8-Week-SQL-Challenge/assets/169105426/1f09079e-66a0-4360-ad91-ce87915a14a3 >

## Case Study Questions

1. What is the total amount each customer spent at the restaurant?
2. How many days has each customer visited the restaurant?
3. What was the first item from the menu purchased by each customer?
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
5. Which item was the most popular for each customer?
6. Which item was purchased first by the customer after they became a member?
7. Which item was purchased just before the customer became a member?
8. What is the total items and amount spent for each member before they became a member?
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

## Bonus Questions
### Join All The Things

The following questions are related creating basic data tables that Danny and his team can use to quickly derive insights without needing to join the underlying tables using SQL.

Recreate the following table output using the available data:

|customer_id|order_date|product_name|price|member|
|:---------:|:--------:|:----------:|:---:|:----:|
|A|2021-01-01|curry|15|N|
|A|2021-01-01|sushi|10|N|
|A|2021-01-07|curry|15|Y|
|A|2021-01-10|ramen|12|Y|
|A|2021-01-11|ramen|12|Y|
|A|2021-01-11|ramen|12|Y|
|B|2021-01-01|curry|15|N|
|B|2021-01-02|curry|15|N|
|B|2021-01-04|sushi|10|N|
|B|2021-01-11|sushi|10|Y|
|B|2021-01-16|ramen|12|Y|
|B|2021-02-01|ramen|12|Y|
|C|2021-01-01|ramen|12|N|
|C|2021-01-01|ramen|12|N|
|C|2021-01-07|ramen|12|N|

#### :arrow_forward: [Solution](https://github.com/ducpham131/8-Week-SQL-Challenge/blob/main/Case%20Study%201%20-%20Danny's%20Diner/Bonus%20Question.md#join-all-the-things)
---
### Rank All The Things
Danny also requires further information about the `ranking` of customer products, but he purposely does not need the ranking for non-member purchases so he expects null `ranking` values for the records when customers are not yet part of the loyalty program.

|customer_id|order_date|product_name|price|member|ranking|
|:---------:|:--------:|:----------:|:---:|:----:|:-----:|
|A|2021-01-01|curry|15|N|null|
|A|2021-01-01|sushi|10|N|null|
|A|2021-01-07|curry|15|Y|1|
|A|2021-01-10|ramen|12|Y|2|
|A|2021-01-11|ramen|12|Y|3|
|A|2021-01-11|ramen|12|Y|3|
|B|2021-01-01|curry|15|N|null|
|B|2021-01-02|curry|15|N|null|
|B|2021-01-04|sushi|10|N|null|
|B|2021-01-11|sushi|10|Y|1|
|B|2021-01-16|ramen|12|Y|2|
|B|2021-02-01|ramen|12|Y|3|
|C|2021-01-01|ramen|12|N|null|
|C|2021-01-01|ramen|12|N|null|
|C|2021-01-07|ramen|12|N|null|

#### :arrow_forward: [Solution](https://github.com/ducpham131/8-Week-SQL-Challenge/blob/main/Case%20Study%201%20-%20Danny's%20Diner/Bonus%20Question.md#rank-all-the-things)
