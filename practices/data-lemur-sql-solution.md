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