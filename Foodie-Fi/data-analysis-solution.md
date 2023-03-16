### Q1: How many customers has Foodie-Fi ever had?
````sql
select
count(distinct(customer_id)) as total_customer
from subscriptions;
````

| total_customer |
|----------------|
| 1000           |


### Q2: What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
````sql
select
strftime('%m', s.start_date) as month,
count(s.customer_id) as count
from subscriptions s
inner join plans p
on s.plan_id = p.plan_id
where plan_name = 'trial'
group by 1;
````

| month | count |
|-------|-------|
| 01    | 88    |
| 02    | 68    |
| 03    | 94    |
| 04    | 81    |
| 05    | 88    |
| 06    | 79    |
| 07    | 89    |
| 08    | 88    |
| 09    | 87    |
| 10    | 79    |
| 11    | 75    |
| 12    | 84    |


### Q3: What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
````sql
with cte as(
  select
  p.plan_name as plan_name,
  p.plan_id as plan_id,
  strftime('%Y', s.start_date) as yr,
  count(customer_id) as count
  from subscriptions s
  inner join plans p
  on s.plan_id = p.plan_id
  group by p.plan_id, 3
  order by 3, p.plan_id
)

select plan_id, plan_name, (count) as '2021'
from cte
where yr = '2021';
````

| plan__id | plan_name     | 2021 |
|----------|---------------|------|
| 1        | basic monthly | 8    |
| 2        | pro monthly   | 60   |
| 3        | pro annual    | 63   |
| 4        | churn         | 71   |


### Q4: What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
````sql
select
total_cust,
round(1.0 * cust_churn / total_cust * 100, 4) || '%'  as churn_percentage
from(
  select
  count(distinct(case when plan_id = 4 then customer_id end)) as cust_churn,
  count(distinct(customer_id)) as total_cust
  from subscriptions
) x;
````

| total_cust | churn_percentage |
|------------|------------------|
| 1000       | 30.7%            |


### Q5: How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
````sql
with churn as(
  select
  customer_id,
  plan_id as prev,
  lead(plan_id) over(partition by customer_id order by plan_id) as current
  from subscriptions
),

churn_rate as(
  select customer_id, prev, current
  from churn
  where prev = 0 and current = 4
)

select
count(customer_id) as churned,
round(1.0 * count(customer_id) / 1000 * 100, 1) || '%' as churn_percentage
from churn_rate;
````

| churned    | churn_percentage |
|------------|------------------|
| 92         | 9.2%             |


### Q6: What is the number and percentage of customer plans after their initial free trial?
````sql
with plan as(
  select
  customer_id,
  plan_id as prev,
  lead(plan_id) over(partition by customer_id order by plan_id) as next_plan
  from subscriptions
)

select
case
	when next_plan = 1 then 'basic monthly'
  when next_plan = 2 then 'pro monthly'
  when next_plan = 3 then 'pro annual'
end as next_plan,
count(customer_id) as count,
round(1.0 * count(customer_id) / 1000 * 100, 2) || '%' as percentage
from plan
where next_plan is not null and next_plan != 4 and prev = 0
group by next_plan;
````

| next_plan     | count     | percentage |
|---------------|-----------|------------|
| basic monthly | 546       | 54.6%      |
| pro monthly   | 325       | 32.5%      |
| pro annual    | 37        | 3.7%       |


### Q7: How many customers have upgraded to an annual plan in 2020?
````sql
select
count(distinct(customer_id)) as total
from subscriptions
where strftime('%Y', start_date) = '2020' and plan_id = 3;
````

| total |
|-------|
| 195   |
