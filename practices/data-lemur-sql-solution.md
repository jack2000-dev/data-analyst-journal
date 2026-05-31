---
Created: 2026-04-24
tags:
  - sql
  - practice
---
# Introduction
[DataLemur](https://datalemur.com/questions?category=SQL): Practice the most common SQL, Statistics, ML, and Python questions asked in FAANG Data Science & Data Analyst interviews! I include my mistakes (The first and latest tries) and errors too to keep it real and learn from the mistakes, and in case I look back to see my track.

## Progress (Before)

![[data-lemur-progress-snapshot-before@2x.png]]

# SQL & Data Interview Questions

## Histogram of Tweets

```SQL
-- Error
SELECT 
  user_id,
  COUNT(tweet_id) AS tweet 
FROM tweets
WHERE tweet_date BETWEEN '2022-01-01' AND '2022-12-31'
GROUP BY user_id
```

```SQL
-- Solved
WITH total_tweets AS (
  SELECT
  user_id,
  COUNT(tweet_id) AS tweet_count_per_user
FROM tweets
WHERE tweet_date BETWEEN '2022-01-01' 
  AND '2022-12-31'
GROUP BY user_id
)

SELECT
  tweet_count_per_user AS tweet_bucket,
  COUNT(user_id) AS users_num
FROM total_tweets
GROUP BY tweet_count_per_user;
```

## Data Science Skills

```SQL
-- Error
SELECT 
  DISTINCT candidate_id
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
```

```SQL
-- Solved
SELECT 
  candidate_id
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(DISTINCT skill) = 3
```

## Page With No Likes

```SQL
-- Error
SELECT 
  p.page_id
FROM pages p
LEFT JOIN page_likes pl ON p.page_id = pl.page_id
GROUP BY p.page_id
HAVING COUNT(pl.liked_date) = 0
```

```SQL
-- Solved
SELECT 
  p.page_id
FROM pages p
LEFT JOIN page_likes pl ON p.page_id = pl.page_id
GROUP BY p.page_id
HAVING COUNT(pl.page_id) = 0
ORDER BY p.page_id ASC;
```

## Unfinished Parts

```SQL
-- Solved
SELECT 
  part,
  assembly_step
FROM parts_assembly
WHERE finish_date ISNULL
```

*Note: First time using `ISNULL` to find an empty value*
## Laptop vs. Mobile Viewership

```SQL
-- Error
WITH laptop_sum AS (
  SELECT 
    user_id,
    COUNT (view_time) AS laptop_reviews
  FROM viewership
  WHERE device_type = 'laptop'
  GROUP BY user_id
),
mobile_sum AS (
  SELECT
    user_id,
    COUNT (view_time) AS mobile_views
    FROM viewership
    WHERE device_type IN ('phone', 'tablet')
    GROUP BY user_id
)

SELECT 
  SUM(laptop_reviews) AS laptop_views,
  SUM(mobile_views)
FROM laptop_sum, mobile_sum
```

```SQL
-- Solved
SELECT
  SUM(CASE 
  WHEN device_type = 'laptop' THEN 1 ELSE 0 END
  ) AS laptop_views,
  SUM(CASE 
  WHEN device_type IN ('phone', 'tablet') THEN 1 ELSE 0 END
  ) AS mobile_views
FROM viewership
```

## Average Post Hiatus (Part 1)

```SQL
-- Error
SELECT 
  user_id,
  ceil(EXTRACT(julian FROM post_date)) - floor(EXTRACT(julian FROM post_date)) AS days_between
FROM posts
WHERE 
  post_date BETWEEN '2021-01-01' AND '2021-12-31'
GROUP BY user_id, days_between
HAVING COUNT(post_id) >= 2
```

```SQL
-- Solved
SELECT 
  user_id,
  MAX(post_date)::date - MIN(post_date)::date  AS days_between
FROM posts
WHERE 
  post_date BETWEEN '2021-01-01' AND '2021-12-31'
GROUP BY user_id
HAVING COUNT(post_id) >= 2
```

*Note: To find the difference between `days` use `MAX() - MIN()` function and to parse only days from datetime column use `::date` *

## Teams Power Users

```SQL
-- Solved
SELECT
  sender_id,
  COUNT(message_id) AS message_count
FROM messages
WHERE sent_date BETWEEN '2022-08-01' AND '2022-08-31'
GROUP BY sender_id
ORDER BY message_count DESC
LIMIT 2
```

## Duplicate Job Listings

```SQL
-- Error
WITH duplicate AS (
  SELECT
  COUNT(j1.job_id + j2.job_id) AS duplicate_companies
FROM job_listings j1
JOIN job_listings j2 ON j1.job_id = j2.job_id
WHERE j1.title = j2.title
GROUP BY j1.company_id
)

SELECT SUM(duplicate_companies) AS duplicate_companies
FROM duplicate
```

```SQL
-- Solved
WITH duplicate AS (
  SELECT
    COUNT(DISTINCT company_id) AS company
  FROM job_listings
  GROUP BY company_id, title
  HAVING COUNT(*) > 1
)
SELECT
  SUM(company) AS duplicate_companies
FROM duplicate
```

## Cities With Completed Trades

```SQL
-- Error
SELECT
  u.city,
  COUNT(t.order_id) AS total_orders
FROM trades t
JOIN users u ON t.user_id = u.user_id
WHERE t.status = 'Completed'
GROUP BY u.user_id, u.city
ORDER BY total_orders DESC
LIMIT 3;
```

```SQL
SELECT
  u.city,
  COUNT(t.order_id) AS total_orders
FROM trades t
JOIN users u ON t.user_id = u.user_id
WHERE t.status = 'Completed'
GROUP BY u.city
ORDER BY total_orders DESC
LIMIT 3;
```

## Average Review Ratings

```SQL
-- Error
SELECT
  EXTRACT(MONTH FROM submit_date) AS mth,
  ROUND(product_id, 2) AS product,
  ROUND(AVG(stars), 2) AS avg_stars
FROM reviews
GROUP BY mth, product
```

```SQL
SELECT
  EXTRACT(MONTH FROM submit_date) AS mth,
  product_id AS product,
  ROUND(AVG(stars), 2) AS avg_stars
FROM reviews
GROUP BY mth, product_id
ORDER BY mth, product
```

## Well Paid Employees

```SQL
-- Error
WITH manager AS (
  SELECT
    manager_id,
    name,
    salary AS manager_salary
  FROM employee
  WHERE manager_id IS NOT NULL
)

SELECT
  e.employee_id,
  e.name AS employee_name
  FROM employee e
  JOIN manager m ON e.employee_id = m.manager_id
  WHERE salary > manager_salary
  GROUP BY e.employee_id, employee_name
```

```SQL
SELECT
  e.employee_id,
  e.name AS employee_name
FROM employee e
JOIN employee m ON e.manager_id = m.employee_id
WHERE e.salary > m.salary
GROUP BY e.employee_id, employee_name
```

*Note: A better solution for this would be a `self-JOIN`, where I was confused by the JOIN clause at first. In database design, a column named `manager_id` on an employee's row almost always refers to who that employee reports to, not who they are.*

## Final Account Balance

```SQL
-- Error
SELECT
  account_id,
  SUM(amount) AS final_balance
FROM transactions
GROUP BY account_id
```

```SQL
-- Solved

-- Final balance = Deposit - Withdrawal
SELECT
  account_id,
    SUM(CASE WHEN transaction_type = 'Deposit' THEN amount ELSE 0 END) -
    SUM(CASE WHEN transaction_type = 'Withdrawal' THEN amount ELSE 0 END) AS final_balance
FROM transactions
GROUP BY account_id
```

## App Click-through Rate (CTR)

```SQL
-- Error
SELECT
  app_id,
  ROUND(COUNT(CASE WHEN event_type = 'click' THEN 1 ELSE 0 END) /
  COUNT(CASE WHEN event_type = 'impression' THEN 1 ELSE 0 END) * 100.0, 2) AS ctr
FROM events
WHERE timestamp BETWEEN '2022-01-01' AND '2022-12-31'
GROUP BY app_id
```

```SQL
-- Solved
SELECT
  DISTINCT app_id,
  ROUND(100.0 * COUNT(CASE WHEN event_type = 'click' THEN 1 ELSE NULL END) /
  COUNT(CASE WHEN event_type = 'impression' THEN 1 ELSE NULL END), 2) AS ctr
FROM events
WHERE timestamp BETWEEN '2022-01-01' AND '2022-12-31'
GROUP BY app_id
ORDER BY ctr
```

## Second Day Confirmation

```SQL
SELECT user_id
FROM emails e 
JOIN texts t ON e.email_id = t.email_id
WHERE t.signup_action = 'Confirmed' 
AND t.action_date = e.signup_date + INTERVAL '1 day'
```

## IBM db2 Product Analytics

```SQL
-- Solved
WITH query_counts AS (
  SELECT 
    e.employee_id,
    COUNT(DISTINCT q.query_id) AS unique_queries
  FROM employees e
  LEFT JOIN queries q 
    ON e.employee_id = q.employee_id
    AND q.query_starttime >= '2023-07-01' 
    AND q.query_starttime < '2023-10-01'
  GROUP BY e.employee_id
)

SELECT 
  unique_queries,
  COUNT(employee_id) AS employee_count
FROM query_counts
GROUP BY unique_queries
ORDER BY unique_queries;
```

## Cards Issued Difference

```SQL
-- Error
SELECT card_name,
  SUM(CASE WHEN card_name = 'Chase Freedom Flex' THEN issued_amount ELSE 0 END) - 
  SUM(CASE WHEN card_name = 'Chase Sapphire Reserve' THEN issued_amount ELSE 0 END) AS difference
FROM monthly_cards_issued
GROUP BY card_name
ORDER BY difference DESC
```

```SQL
-- Solved
SELECT
  card_name,
  MAX(issued_amount) - MIN(issued_amount) AS difference
FROM monthly_cards_issued
GROUP BY card_name
ORDER BY difference DESC
```

## Compressed Mean

```SQL
-- Solved
SELECT ROUND(
    SUM(item_count * order_occurrences)::DECIMAL / 
    SUM(order_occurrences), 
    1) AS mean
FROM items_per_order;
```
## Pharmacy Analytics (Part 1)

```sql
-- Solved
SELECT
    drug,
    (total_sales - cogs) as total_profit
FROM pharmacy_sales
ORDER BY 2 DESC
LIMIT 3
```

## Pharmacy Analytics (Part 2)

```sql
-- Error
SELECT
    manufacturer,
    COUNT(drug) as drug_count,
    ABS(SUM(cogs - total_sales)) as total_loss
FROM pharmacy_sales
GROUP BY 1
ORDER BY total_loss DESC
```
```sql
-- Solved
SELECT
    manufacturer,
    COUNT(drug) as drug_count,
    ABS(SUM(total_sales - cogs)) as total_loss
FROM pharmacy_sales
WHERE cogs > total_sales
GROUP BY 1
ORDER BY 3 DESC
```

## Phramacy Analytics (Part 3)

```sql
-- Solved
SELECT
  manufacturer,
  CONCAT('$', ROUND(SUM(total_sales) / 1000000 , 0), ' million') as sale
FROM pharmacy_sales
GROUP BY 1
ORDER BY SUM(total_sales) DESC
```
## Patient Support Analysis (Part 1)

```sql
-- Error
SELECT
    count(policy_holder_id) as policy_holder_count
FROM callers
HAVING count(case_id) >= 3
```

```sql
-- Solved
WITH cte as (
    SELECT
      policy_holder_id
    FROM callers
    GROUP BY 1
    HAVING count(case_id) >= 3
)

SELECT
    count(*) as policy_holder_count
FROM cte
```

## User's Third Transaction

```sql
-- Solved
with third_txn as (
  select
    user_id,
    spend,
    transaction_date,
    ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY transaction_date) as tx_num
  from transactions
)

select
  user_id,
  spend,
  transaction_date
from third_txn
where tx_num = 3
```

## Second Highest Salary

```sql
-- Solved
with second_rank as (
    select
    salary,
    dense_rank() over(order by salary desc) as salary_rank
FROM employee
)

select
  salary as second_higest_salary
from second_rank
where salary_rank = 2
```

## Sending vs. Opening Snaps

```sql
-- Solved
with cte as (
  select
      user_id,
      case when activity_type = 'send' then time_spent else 0 end as send,
      case when activity_type = 'open' then time_spent else 0 end as open
  from activities
)

select
    a.age_bucket,
    round(100 * sum(send) / (sum(send) + sum(open)), 2) as send_perc,
    round(100 * sum(open) / (sum(send) + sum(open)), 2) as open_perc
from cte
join age_breakdown a on cte.user_id = a.user_id
group by a.age_bucket
```

## Tweets' Rolling Averages

```sql
-- Error
with ma as (
SELECT 
    user_id,
    tweet_date,
    avg(tweet_count) over (
      order by tweet_date
      rows between 2 preceding and current row
    ) as rolling_avg_3d
FROM tweets
)

select
    user_id,
    tweet_date,
    round(rolling_avg_3d, 2) as rolling_avg_3d
from ma
order by user_id
```

```sql
-- Solved
with ma as (
SELECT 
    user_id,
    tweet_date,
    avg(tweet_count) over (
      partition by user_id
      order by tweet_date
      rows between 2 preceding and current row
    ) as rolling_avg_3d
FROM tweets
)

select
    user_id,
    tweet_date,
    round(rolling_avg_3d, 2) as rolling_avg_3d
from ma
order by 1, 2
```

## Highest-Grossing Items

```sql
-- Error
SELECT 
  category,
  product,
  sum(spend) as total_spend
FROM product_spend
where extract (year from transaction_date) = 2022
group by 1, 2
order by total_spend desc
```

```sql
-- Solved
with total as (
  select
    category,
    product,
    sum(spend) as total_spend
  from product_spend
  where extract (year from transaction_date) = 2022
  group by 1, 2
),

rank as (
SELECT 
  category,
  product,
  total_spend,
  rank() over (partition by category order by total_spend desc) as rank
FROM total
)

select
  category,
  product,
  total_spend
from rank
where rank <= 2
order by 1, 3 desc
```
## Top Three Salaries

```sql
-- Error
  with rank as (
    SELECT
        dense_rank() over (partition by d.department_id order by e.name) as rank_salary,
        d.department_name,
        e.name,
        e.salary
    FROM employee e
    join department d on e.department_id = d.department_id
  )
  
  select
    department_name,
    name,
    salary
  from rank
  where rank_salary <= 3
```

```sql
-- Solved
with salary_rank as (
  SELECT
      dense_rank() over (partition by d.department_id order by e.salary desc) as rank_salary,
      d.department_name,
      e.name as employee_name,
      e.salary
  FROM employee e
  join department d on e.department_id = d.department_id
  order by 3
)

select
  department_name,
  employee_name,
  salary
from salary_rank
where rank_salary <= 3
order by 
  department_name asc,
  salary desc, 
  employee_name asc
```

Note: I alias name because it's reserve keyword in SQL. Keep the code clean

## Signup Activation Rate

```sql
-- Error
with total_user as (
  select 
    count(distinct user_id) as total_unique_user
  from emails
),

activated_user as (

SELECT 
   count(distinct user_id) as total_activated_user
FROM emails e 
join texts t on e.email_id = t.email_id
where t.signup_action = 'Confirmed'
)

select
  round(100 * total_activated_user / total_unique_user, 2) as confirm_rate
from activated_user
cross join total_user
```
```sql
-- Solved
with total_user as (
  select 
    count(distinct user_id) as total_unique_user
  from emails
),

activated_user as (

SELECT 
   count(distinct user_id) as total_activated_user
FROM emails e 
join texts t on e.email_id = t.email_id
where t.signup_action = 'Confirmed'
)

select
  round(cast(total_activated_user as numeric) / cast(total_unique_user as numeric), 2) as activation_rate
from activated_user
cross join total_user
```

```sql
-- Optimized version
SELECT
  ROUND(
    COUNT(DISTINCT CASE WHEN t.signup_action = 'Confirmed' THEN e.user_id END)::NUMERIC /
    COUNT(DISTINCT e.user_id)::NUMERIC,
  2) AS activation_rate
FROM emails e
LEFT JOIN texts t ON e.email_id = t.email_id
```