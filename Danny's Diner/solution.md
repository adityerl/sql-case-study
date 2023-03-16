### Q1: What is the total amount each customer spent at the restaurant?
````sql
select
s.customer_id,
sum(m.price) as total_amount
from sales s
inner join menu m
on s.product_id = m.product_id
group by s.customer_id;
````

| customer_id | total_amount |
|-------------|--------------|
| A           | 76           |
| B           | 74           |
| C           | 36           |


### Q2: How many days has each customer visited the restaurant?
````sql
select
customer_id,
count(distinct(order_date)) as day
from sales
group by customer_id;
````

| customer_id | day |
|-------------|-----|
| A           | 4   |
| B           | 6   |
| C           | 2   |


### Q3: What was the first item from the menu purchased by each customer?
````sql
with first_order as(
  select
  s.customer_id as cust,
  m.product_name as prod,
  dense_rank() over(partition by s.customer_id order by order_date) as rnk
  from menu m
  inner join sales s
  on m.product_id = s.product_id
)

select cust, prod
from first_order
where rnk = 1
group by cust, prod;
````

| cust        | prod         |
|-------------|--------------|
| A           | curry        |
| A           | sushi        |
| B           | curry        |
| C           | ramen        |


### Q4 What is the most purchased item on the menu and how many times was it purchased by all customers?
````sql
select prod_name, max(total_purchased) as most_purchased
from (
  select m.product_name as prod_name, count(s.product_id) as total_purchased
  from sales s
  inner join menu m
  on s.product_id = m.product_id
  group by s.product_id
) x;
````

| prod_name   | most_purchased |
|-------------|----------------|
| ramen       | 8              |


### Q5 Which item was the most popular for each customer?
````sql
with most_popular as(
  select
  s.customer_id cust_id,
  m.product_name as prod_name,
  count(s.product_id) as order_count,
  dense_rank() over(partition by s.customer_id order by count(s.product_id) desc) as rnk
  from sales s
  inner join menu m
  on s.product_id = m.product_id
  group by s.customer_id, s.product_id
)

select cust_id, prod_name, order_count
from most_popular
where rnk = 1;
````

| cust_id     | prod_name    | order_count |
|-------------|--------------|-------------|
| A           | ramen        | 3           |
| B           | ramen        | 2           |
| B           | curry        | 2           |
| B           | sushi        | 2           |
| C           | ramen        | 3           |
