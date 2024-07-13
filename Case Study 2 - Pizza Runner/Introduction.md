# Case Study #2 - Pizza Runner
<img src="https://8weeksqlchallenge.com/images/case-study-designs/2.png" width="500">

## Introduction
Fueled by a vision of "80s Retro Styling and Pizza Is The Future!", Danny knew conquering the pizza world required more than just delicious pies. Seed funding for a pizza empire seemed out of reach, so Danny hatched a brilliant plan: Uberize it!

Thus, Pizza Runner was born. Danny's house became "Pizza Runner Headquarters," and he assembled a team of "runners" for speedy delivery. He even maxed out his credit card to build a mobile app, transforming his dream into a fledgling delivery service.

*Source - https://8weeksqlchallenge.com/case-study-2/*

## Available Data
Danny has prepared for us an entity relationship diagram of his database design but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operations.

There are 6 tables in this dataset:
- [runners](#table-1-runners)
- [customer_orders](#table-2-customer_orders)
- [runner_orders](#table-3-runner_orders)
- [pizza_names](#table-4-pizza_names)
- [pizza_recipes](#table-5-pizza_recipes)
- [pizza_toppings](#table-6-pizza_toppings)

### Table 1: runners
The **`runners`** table shows the **`registration_date`** for each new runner

|runner_id|registration_date|
|:-------:|:---------------:|
|1|2021-01-01|
|2|2021-01-03|
|3|2021-01-08|
|4|2021-01-15|

### Table 2: customer_orders
Customer pizza orders are captured in the **`customer_orders`** table with 1 row for each individual pizza that is part of the order.

The **`pizza_id`** relates to the type of pizza which was ordered whilst the **`exclusions`** are the **`ingredient_id`** values which should be removed from the pizza and the **`extras`** are the **`ingredient_id`** values which need to be added to the pizza.

Note that customers can order multiple pizzas in a single order with varying **`exclusions`** and **`extras`** values even if the pizza is the same type!

The **`exclusions`** and **`extras`** columns will need to be cleaned up before using them in your queries.

| order_id | customer_id | pizza_id | exclusions | extras | order_time               |
| -------- | ----------- | -------- | ---------- | ------ | ------------------------ |
| 1        | 101         | 1        |            |        | 2020-01-01T18:05:02.000Z |
| 2        | 101         | 1        |            |        | 2020-01-01T19:00:52.000Z |
| 3        | 102         | 1        |            |        | 2020-01-02T23:51:23.000Z |
| 3        | 102         | 2        |            |        | 2020-01-02T23:51:23.000Z |
| 4        | 103         | 1        | 4          |        | 2020-01-04T13:23:46.000Z |
| 4        | 103         | 1        | 4          |        | 2020-01-04T13:23:46.000Z |
| 4        | 103         | 2        | 4          |        | 2020-01-04T13:23:46.000Z |
| 5        | 104         | 1        | null       | 1      | 2020-01-08T21:00:29.000Z |
| 6        | 101         | 2        | null       | null   | 2020-01-08T21:03:13.000Z |
| 7        | 105         | 2        | null       | 1      | 2020-01-08T21:20:29.000Z |
| 8        | 102         | 1        | null       | null   | 2020-01-09T23:54:33.000Z |
| 9        | 103         | 1        | 4          | 1, 5   | 2020-01-10T11:22:59.000Z |
| 10       | 104         | 1        | null       | null   | 2020-01-11T18:34:49.000Z |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2020-01-11T18:34:49.000Z |

### Table 3: runner_orders
After each orders are received through the system - they are assigned to a runner - however not all orders are fully completed and can be cancelled by the restaurant or the customer.

The **`pickup_time`** is the timestamp at which the runner arrives at the Pizza Runner headquarters to pick up the freshly cooked pizzas. The **`distance`** and **`duration`** fields are related to how far and long the runner had to travel to deliver the order to the respective customer.

| order_id | runner_id | pickup_time         | distance | duration   | cancellation            |
| -------- | --------- | ------------------- | -------- | ---------- | ----------------------- |
| 1        | 1         | 2020-01-01 18:15:34 | 20km     | 32 minutes |                         |
| 2        | 1         | 2020-01-01 19:10:54 | 20km     | 27 minutes |                         |
| 3        | 1         | 2020-01-03 00:12:37 | 13.4km   | 20 mins    |                         |
| 4        | 2         | 2020-01-04 13:53:03 | 23.4     | 40         |                         |
| 5        | 3         | 2020-01-08 21:10:57 | 10       | 15         |                         |
| 6        | 3         | null                | null     | null       | Restaurant Cancellation |
| 7        | 2         | 2020-01-08 21:30:45 | 25km     | 25mins     | null                    |
| 8        | 2         | 2020-01-10 00:15:02 | 23.4 km  | 15 minute  | null                    |
| 9        | 2         | null                | null     | null       | Customer Cancellation   |
| 10       | 1         | 2020-01-11 18:50:20 | 10km     | 10minutes  | null                    |

### Table 4: pizza_names
At the moment - Pizza Runner only has 2 pizzas available the Meat Lovers or Vegetarian!

|pizza_id|pizza_name|
|:------:|:--------:|
|1|Meat Lovers|
|2|Vegetarian|

### Table 5: pizza_recipes
Each **`pizza_id`** has a standard set of **`toppings`** which are used as part of the pizza recipe.

|pizza_id|toppings|
|:------:|:------:|
|1|1, 2, 3, 4, 5, 6, 8, 10|
|2|4, 6, 7, 9, 11, 12|

### Table 6: pizza_toppings
This table contains all of the **`topping_name`** values with their corresponding topping_id value
| topping_id | topping_name |
| ---------- | ------------ |
| 1          | Bacon        |
| 2          | BBQ Sauce    |
| 3          | Beef         |
| 4          | Cheese       |
| 5          | Chicken      |
| 6          | Mushrooms    |
| 7          | Onions       |
| 8          | Pepperoni    |
| 9          | Peppers      |
| 10         | Salami       |
| 11         | Tomatoes     |
| 12         | Tomato Sauce |
