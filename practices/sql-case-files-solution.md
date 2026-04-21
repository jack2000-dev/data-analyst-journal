---
Created: 2026-03-25
tags:
  - sql
  - practice
---
# Introduction

[SQL CASE FILES](https://sqlcasefiles.com/) - A SQL game for interactively practicing your SQL skills. It contains 10 case files with 10 quizzes in each one, totaling 100 quizzes. Below is my journey practicing and having fun with the game. If I make a mistake, it will be corrected with another snippet of SQL code.
# CASE FILE: S01 - The Rookie Files

![S01 The Rookie Files](../img/sql-case-files-img/S01-TheRookieFiles.png)

## Solution

**1) The Usual Suspects**<br>
The Captain needs a roster of potential threats. Retrieve the `name` of every individual currently listed in the `suspects` database.

```SQL
SELECT * FROM suspects
```

**2) Profiling the Perpetrators**<br>
We need to understand who we are dealing with. Retrieve the `name` and `age` for every person in the `suspects` file to build their profiles.

```SQL
SELECT name, age FROM suspects
```

**3) The Victim Pool**<br>
The Chief wants to see the raw data. Retrieve every column (`*`) for all records in the `victims` database so we can review the complete reports.

```SQL
SELECT * FROM victims
```

**4) Scene of the Crime**<br>
Activity is spiking in Union Square. Isolate the reports by finding all incidents where the `location` is 'Union Square'.

```SQL
SELECT * FROM incidents WHERE location = 'Union Square'
```

**5) Juvenile Delinquents**<br>
Intel suggests they are recruiting kids. List the full records of all `suspects` with an `age` less than 18 to identify the minors.

```SQL
SELECT * FROM suspects WHERE age < 18
```

**6) Mapping the Territory**<br>
We need to map their territory. Retrieve a list of unique `location` names from the `incidents` table to see exactly where they operate.

```SQL
SELECT DISTINCT location FROM incidents
```

**7) Standardizing Intelligence**<br>
The DA demands the file be practically court-ready. Retrieve the `name` of every suspect, but alias the column to `Suspect_Name` to match the official paperwork.

```SQL
SELECT name AS Suspect_Name FROM suspects
```

**8) The Latest Hits**<br>
Time is of the essence. Focus on the immediate threat by retrieving the 3 most recent incident reports, sorted by `timestamp` from newest to oldest.

```SQL
SELECT *
FROM incidents
ORDER BY timestamp DESC
LIMIT 3
```

**9) Corroborating the Story**<br>
We need to break his alibi. Find the incident record that places a suspect at 'Fishermans Wharf' (`location`) AND matches `suspect_id` 3.

```SQL
SELECT *
FROM suspects AS s
JOIN incidents AS i ON i.suspect_id = s.suspect_id
WHERE i.location = 'Fishermans Wharf'
AND s.suspect_id = 3
```

**10) The Arrest Warrant**<br>
The judge is ready to sign. Retrieve the complete suspect profile for the individual named 'Tyler Johnson' so we can secure the warrant.

```SQL
SELECT *
FROM suspects
WHERE name = 'Tyler Johnson'
```

# CASE FILE: S02 - Payroll Poison

![CASE FILE S02 - Payroll Poison](../img/sql-case-files-img/CASE%20FILE%20S02%20-%20Payroll%20Poison.png)

## Solution

**11) Headcount**<br>
First, let's establish a baseline. Find the total number of employees currently on the official roster and alias the result as `total_employees` so we can compare it against the payroll records.

```SQL
SELECT COUNT(employee_id)
FROM employees
```

**12) The Budget Bleed**<br>
The budget is bleeding dry. Calculate the total monthly expenditure from the `payroll` table and alias it as `total_payroll` to see exactly how much cash is going out the door.

```SQL
SELECT SUM(gross_monthly_pay) AS total_payroll
FROM payroll
```

**13) Department Drift**<br>
A total doesn't tell the whole story. Breakdown the spending by `department`, showing the sum of salaries (aliased as `total_salary`) for each one. Sort by `total_salary` from highest to lowest to identify the biggest burn.

```SQL
SELECT department, SUM(annual_salary) AS total_salary
FROM employees
GROUP BY department -- If you don't use this line it will not show all departments
ORDER BY total_salary DESC
```

**14) The Bloated Budget**<br>
We suspect 'Administration' is padding their numbers. Flag any department with a total `annual_salary` exceeding $750,000. Return the `department` and `total_salary` so we can investigate.

```SQL
SELECT department, SUM(annual_salary) AS total_salary
FROM employees
GROUP BY department
HAVING SUM(annual_salary) > 750000 -- WHERE cannot be used to filter aggregate result
```

**15) Ghost Payment**<br>
The system shows 'inactive' employees, but money is still moving. Trace the payments by linking every employee to their payroll record. Select the employee's `name` and `gross_monthly_pay`, sorted by name.

```SQL
SELECT name, gross_monthly_pay
FROM employees AS e
JOIN payroll AS p ON p.employee_id = e.employee_id
ORDER BY e.name
```

**16) Paying the Dead**<br>
Gotcha. List the unique `name` and `gross_monthly_pay` for every employee who is currently marked 'inactive' but still receiving a paycheck, sorted by name. This is our smoking gun.

```SQL
SELECT DISTINCT name, gross_monthly_pay, status
FROM employees AS e
JOIN payroll AS p ON e.employee_id = p.employee_id
WHERE status = 'inactive'
ORDER BY name
```

**17) The Inside Man**<br>
Let's see if this is just an outlier or a pattern. Calculate the average `annual_salary` for the 'Administration' department, aliasing the result as `average_salary`, to see if they're inflating pay across the board.

```SQL
SELECT department, AVG(annual_salary) AS average_salary
FROM employees
WHERE department = 'Administration'
```

**18) Highest Earner**<br>
We need to know who sits at the top of this pyramid. Find the maximum `annual_salary` in the organization and alias it as `highest_salary`.

```SQL
SELECT employee_id, name, MAX(annual_salary) AS highest_salary
FROM employees
```

**19) Lowest on the Totem Pole**<br>
Contrast is key. Establish the floor by finding the minimum `annual_salary` on the books and aliasing it as `lowest_salary`.

```SQL
SELECT employee_id, name, MIN(annual_salary) AS lowest_salary
FROM employees
```

**20) The Poison Pill**<br>
We have our ghost: Marcus Williams (Employee #15). Quantify the theft by calculating the total sum of `gross_monthly_pay` issued to him, aliasing the total as `total_fraud_amount`. We need a dollar figure for the indictment.

```SQL
-- My query does not match the correct answer, but the logic was right. I was basically doing too much. The next snippet is more efficient.
SELECT e.employee_id,
    e.name,
    '$' || SUM(gross_monthly_pay) AS total_fraud_amount
FROM employees AS e
JOIN payroll AS p ON p.employee_id = e.employee_id
WHERE e.employee_id = 15
```

```SQL
/* The correct version (provide the same logical result with more efficiency since there is no need to use JOIN in this situation.
*/
SELECT SUM(gross_monthly_pay) AS total_fraud_amount 
FROM payroll 
WHERE employee_id = 15;
```

# CASE FILE: S03 - Double Dealer

![CASE FILE S03 - Double Dealer](../img/sql-case-files-img/CASE%20FILE%20S03%20-%20Double%20Dealer.png)
## Solution

**21) The Vendor Network**<br>
An anonymous tip suggests a cartel. Map the network by linking every vendor to their contracts. Retrieve the vendor `name` and the contract `item` for every deal on file, sorted by vendor name.

```SQL
SELECT name, item
FROM vendors AS v
JOIN contracts AS c ON c.vendor_id = v.vendor_id
ORDER BY v.name
```

**22) The Money Trail**<br>
Follow the paper trail. Retrieve the vendor `name`, `contact_person`, and contract `amount` for every signed agreement, sorted by vendor name, so we can identify the key beneficiaries.

```SQL
SELECT v.name, v.contact_person, c.amount, c.signed_date
FROM vendors AS v
JOIN contracts AS c ON c.vendor_id = v.vendor_id
ORDER BY v.name
```

**23) The Delivery Problem**<br>
We need to connect the dots. List the vendor `name` and contract `item` for every shipment currently marked as 'Missing', sorted by vendor name, to see who is failing to deliver.

```SQL
SELECT v.name, c.item, s.status
FROM vendors AS v
JOIN contracts AS c ON c.vendor_id = v.vendor_id
JOIN shipments AS s ON s.contract_id = c.contract_id
WHERE s.status = 'Missing'
ORDER BY v.name
```

**24) The Repeat Offender**<br>
QuickShip keeps appearing. Audit the low-rated vendors by listing the `name` and contract `amount` for any vendor with a `rating` of 2 or lower. Sort by contract amount from highest to lowest.

```SQL
SELECT v.name, c.amount
FROM vendors AS v
JOIN contracts AS c ON c.vendor_id = v.vendor_id
WHERE v.rating <= 2
ORDER BY c.amount DESC
```

**25) The Inside Connection**<br>
Let's assume Phantom is involved. Reconstruct the timeline by listing the `item` and `signed_date` for every contract awarded to 'Phantom Enterprises', sorted chronologically.

```SQL
SELECT v.name, c.item, c.signed_date
FROM contracts AS c
JOIN vendors AS v ON v.vendor_id = c.vendor_id
WHERE v.name = 'Phantom Enterprises'
ORDER BY c.signed_date ASC
```

**26) The High-Value Targets**<br>
Let's see the big picture. Calculate the total value of all contracts awarded to each vendor, aliasing the sum as `total_contract_value`. Sort by this value from highest to lowest to see who tops the list.

```SQL
SELECT v.name, SUM(c.amount) AS total_contract_value
FROM contracts AS c
JOIN vendors AS v ON v.vendor_id = c.vendor_id
GROUP BY v.name
ORDER BY total_contract_value DESC
```

**27) The Delivery Rate**<br>
Now let's look at performance. Count the number of 'Delivered' shipments for each vendor, aliasing the result as `delivered_count`, sorted alphabetically. We need to know who actually does the work.

```SQL
/*
My query is incorrect because I mistakenly perceive `delivered_count` as delivered result (s.status). But the INNER JOIN was on point!
*/

SELECT DISTINCT v.name, COUNT(s.status) = 'Delivered' AS delivered_count
FROM vendors AS v
JOIN contracts AS c ON v.vendor_id = c.vendor_id
JOIN shipments AS s ON c.contract_id = s.contract_id
GROUP BY v.name
ORDER BY v.name ASC
```

```SQL
-- The correct answer
SELECT v.name, COUNT(s.shipment_id) AS delivered_count 
FROM vendors v 
JOIN contracts c ON v.vendor_id = c.vendor_id 
JOIN shipments s ON c.contract_id = s.contract_id 
WHERE s.status = 'Delivered' 
GROUP BY v.vendor_id, v.name 
ORDER BY v.name;
```

**28) The Contact Network**<br>
Expose the shell game. Find any `contact_person` linked to multiple different vendor records. List their `contact_person` name and the two vendor names (aliased as `vendor1` and `vendor2`). Ensure each pair is listed only once (use `a.name < b.name`).

```SQL
-- My incorrect answer. This is called 'Self JOIN'
SELECT a.contact_person AS vendor1,
       b.contact_person AS vendor2
FROM vendors a, vendors b
WHERE a.name < b.name
```

```SQL
-- The correct answer
SELECT DISTINCT a.contact_person,
a.name AS vendor1, 
b.name AS vendor2 
FROM vendors a 
JOIN vendors b ON a.contact_person = b.contact_person 
WHERE a.vendor_id != b.vendor_id 
AND a.name < b.name;
```

**29) The Timeline Analysis**<br>
Let's check the timing. Analyze the disappearance timeline by listing the contract `item` and `signed_date` for all shipments with a 'Missing' status, sorted by date.

```SQL
SELECT c.item, c.signed_date
FROM contracts AS c
JOIN shipments AS s ON s.contract_id = c.contract_id
WHERE s.status = 'Missing'
ORDER BY c.signed_date ASC
```

**30) The Final Connection**<br>
This is it. Build the final case file for the DA. Generate a report for all high-value contracts (over $50,000), listing the vendor `name`, `contact_person`, contract `amount`, and shipment `status`, ranked by amount from highest to lowest.

```SQL
SELECT v.name,
       v.contact_person,
       c.amount,
       s.status
FROM vendors v
JOIN contracts c ON c.vendor_id = v.vendor_id
JOIN shipments s ON s.contract_id = c.contract_id
WHERE c.amount > 50000
ORDER BY c.amount DESC
```

# CASE FILE: S04 - Disappearing Cargo

![CASE FILE S04 - Disappearing Cargo](../img/sql-case-files-img/CASE%20FILE%20S04%20-%20Disappearing%20Cargo.png)

## Solution

**31) The Missing Shipments**

The warehouse manager is panicking. Show me exactly what's gone. List the `po_id` and `item_description` for every purchase order that has no corresponding delivery record, sorted by `po_id`.

```SQL
-- My incorrect answer
SELECT p.po_id, p.item_description 
FROM purchase_orders AS p 
JOIN deliveries d ON d.po_id = p.po_id 
JOIN inventory i ON i.po_id = d.po_id 
WHERE p.po_id IS NULL
ORDER BY p.po_id
```

```SQL
-- The correct answer
SELECT 
    p.po_id, 
    p.item_description
FROM purchase_orders AS p
LEFT JOIN deliveries d ON p.po_id = d.po_id
WHERE d.po_id IS NULL
ORDER BY p.po_id;
```

**32) The Phantom Vendors**

We suspect ghost vendors. Expose them by listing the `vendor_name` and `item_description` for every order that was never delivered, sorted by `vendor_name`.

```SQL
SELECT p.vendor_name,
       p.item_description
FROM deliveries AS d
RIGHT JOIN purchase_orders p ON d.po_id = p.po_id
WHERE d.delivery_id IS NULL
ORDER BY p.vendor_name
```

**33) The Value of Nothing**

The Chief needs a number. Quantify the loss by calculating the total value of all undelivered orders (quantity * unit_price) and alias it as `missing_value`.

```SQL
SELECT SUM(quantity * unit_price) AS missing_value
FROM deliveries AS d
RIGHT JOIN purchase_orders p ON d.po_id = p.po_id
WHERE d.delivery_id IS NULL
```

**34) Partial Deliveries**

Interrogation revealed they are skimming off the top. Detect this by listing the `po_id`, `item_description`, `quantity`, and `delivered_quantity` for any order where the delivered amount is less than ordered. Sort by `po_id`.

```SQL
SELECT p.po_id,
       p.item_description,
       p.quantity,
       d.delivered_quantity
FROM purchase_orders AS p
JOIN deliveries d ON d.po_id = p.po_id
WHERE d.delivered_quantity < p.quantity
ORDER BY p.po_id
```

**35) The Inventory Discrepancy**

It's an inside job. Trace the internal theft by listing the `delivery_id` and `item_description` for any shipment that was delivered but never scanned into inventory. Sort by `delivery_id`.

```SQL
SELECT d.delivery_id, p.item_description
FROM purchase_orders AS p
JOIN deliveries d ON d.po_id = p.po_id
LEFT JOIN inventory i ON i.po_id = d.po_id
WHERE i.last_audit IS NULL
ORDER BY d.delivery_id
```

**36) Stock Shrinkage**

The books are cooked. Audit the stock levels by listing the `item_description`, `expected_stock`, and `current_stock` for items where `current_stock` is less than expected. Calculate the difference as `missing`, sorted by `item_description`.

```SQL
SELECT p.item_description, i.expected_stock, i.current_stock, i.expected_stock - i.current_stock AS missing
FROM purchase_orders AS p
JOIN deliveries d ON d.po_id = p.po_id
JOIN inventory i ON i.po_id = d.po_id
WHERE i.current_stock < i.expected_stock
ORDER BY p.item_description
```

**37) The Timeline Gap**

We need to find when the breakdown started. Analyze the timeline gaps by listing the `vendor_name`, `order_date`, and `delivery_date` for every purchase order, sorted by `order_date`.

```SQL
SELECT p.vendor_name, p.order_date, d.delivery_date
FROM purchase_orders AS p
FULL JOIN deliveries d ON d.po_id = p.po_id
ORDER BY p.order_date
```

**38) The Warehouse Leak**

Pinpoint the leaks. Rank the warehouses by loss, showing the `target_warehouse` and the count of **missing** deliveries (`missing_shipments`) for each. Order by `missing_shipments` from highest to lowest.

```SQL
-- My incorrect answer
SELECT p.target_warehouse, COUNT(i.expected_stock - i.current_stock) AS missing_shipments
FROM purchase_orders AS p
FULL JOIN deliveries d ON d.po_id = p.po_id
JOIN inventory i ON i.po_id = d.po_id
GROUP BY p.target_warehouse
ORDER BY missing_shipments DESC
```

```SQL
-- The correct answer
SELECT po.target_warehouse, 
COUNT(po.po_id) AS missing_shipments 
FROM purchase_orders po 
LEFT JOIN deliveries d ON po.po_id = d.po_id
WHERE d.delivery_id IS NULL
GROUP BY po.target_warehouse
ORDER BY missing_shipments DESC;
```

**39) The Vendor Scorecard**

We need a performance review. Scorecard the vendors by listing the `vendor_name`, `total_orders`, `successful_deliveries`, and `total_value`. Rank by successful deliveries, highest to lowest.

```SQL
-- My incorrect answer
SELECT p.vendor_name,
       SUM(p.quantity) AS total_orders,
       COUNT(p.quantity) - 
       COUNT(d.delivered_quantity) AS successful_deliveries,
       p.unit_price * p.quantity AS total_value
FROM purchase_orders AS p
LEFT JOIN deliveries d ON d.po_id = p.po_id
GROUP BY p.vendor_name
ORDER BY successful_deliveries DESC
```

```SQL
-- The correct answer
SELECT po.vendor_name, 
       COUNT(po.po_id) AS total_orders,
       COUNT(d.delivery_id) AS successful_deliveries,
       SUM(po.quantity * po.unit_price) AS total_value 
FROM purchase_orders po 
LEFT JOIN deliveries d ON po.po_id = d.po_id 
GROUP BY po.vendor_name 
ORDER BY successful_deliveries DESC;
```

**40) The Final Audit**

The DA needs everything. Compile the final audit report listing `po_id`, `vendor_name`, `item_description`, `order_value`, `delivery_status`, and `inventory_loss`. Rank by order value descending.

```SQL
-- My incorrect answer
SELECT p.po_id,
       p.vendor_name,
       p.item_description,
       p.quantity * p.unit_price AS order_value,
       COUNT(d.delivery_id) AS delivery_status,
       i.expected_stock - i.current_stock AS inventory_loss
FROM purchase_orders AS p
LEFT JOIN deliveries d ON d.po_id = p.po_id
LEFT JOIN inventory i ON i.po_id = d.po_id
GROUP BY p.vendor_name
ORDER BY order_value DESC;
```

```SQL
-- The correct answer
SELECT po.po_id, 
       po.vendor_name,
       po.item_description,
       po.quantity * po.unit_price AS order_value,
       CASE WHEN d.delivery_id IS NULL 
       THEN 'Not Delivered' ELSE 'Delivered' END AS delivery_status, COALESCE(i.expected_stock - i.current_stock, 0) AS inventory_loss 
FROM purchase_orders po
LEFT JOIN deliveries d ON po.po_id = d.po_id
LEFT JOIN inventory i ON po.po_id = i.po_id
ORDER BY order_value DESC;
```

# CASE FILE: S05 - Pattern of Violence

![CASE FILE S05 - Pattern of Violence](../img/sql-case-files-img/CASE%20FILE%20S05%20-%20Pattern%20of%20Violence.png)

## Solution

**41) The Repeat Offender**

It's not random. Someone is hitting the city again and again. Identify the repeat offenders by listing the `suspect_id`, `name`, and `incident_count` for any suspect linked to more than one assault. Sort by `incident_count` descending.

```SQL
SELECT i.suspect_id, s.name, COUNT(i.incident_id) AS incident_count
FROM incidents AS i
JOIN suspects s
ON i.suspect_id = s.suspect_id
GROUP BY s.suspect_id, s.name
HAVING incident_count > 1
ORDER BY incident_count DESC
```

**42) The Weapon Pattern**

We need to understand their MO. Analyze the weapon usage patterns by listing the `weapon_used` and the `usage_count` for each type, ranked by frequency.

```SQL
SELECT i.weapon_used, COUNT(i.weapon_used) AS usage_count
FROM incidents AS i
GROUP BY i.weapon_used
ORDER BY usage_count DESC
```

**43) The Time Hunter**

Serial attackers follow a schedule. Predict the next strike by grouping attacks into 'Night' (22:00-06:00), 'Morning' (06:00-12:00), 'Afternoon' (12:00-18:00), and 'Evening' (18:00-22:00). List the `time_period` and `incident_count`, ranked by frequency.

```SQL
/*
At first, I used BETWEEN, but AI suggested that it might overlap
because we're dealing with TIME data here.

Smarter approch here would be putting 'Evening' to ELSE instead of 'Error'
*/
SELECT
CASE
  WHEN time_of_day >= '22:00' OR time_of_day < '06:00' THEN 'Night' -- Use OR to prevent time overlap issue
  WHEN time_of_day >= '06:00' AND time_of_day < '12:00' THEN 'Morning'
  WHEN time_of_day >= '12:00' AND time_of_day < '18:00' THEN 'Afternoon'
  WHEN time_of_day >= '18:00' AND time_of_day < '22:00' THEN 'Evening'
  ELSE 'Error'
END AS time_period,
COUNT(DISTINCT incident_id) AS incident_count
FROM incidents
GROUP BY time_period
ORDER BY incident_count DESC
```

**44) The Victim Profile**

We need a victimology profile. Categorize victims into 'Young Adult' (< 25), 'Adult' (< 35), and 'Older Adult' (35+). For each group, list the `age_group`, `victim_count`, and `avg_age`, ranked by count.

```SQL
SELECT
CASE
  WHEN victim_age < 25 THEN 'Young Adult'
  WHEN victim_age < 35 THEN 'Adult'
  ELSE 'Older Adult'
END AS age_group,
COUNT(incident_id) AS victim_count,
AVG(victim_age)
FROM incidents
GROUP BY age_group
ORDER BY victim_count DESC
```

**45) The Severity Escalation**

Violence is escalating. Track the trend by listing the `suspect_id`, `name`, `avg_severity`, `max_severity`, and `incident_count` for suspects with multiple incidents. Rank by average severity to catch the worst offenders.

```SQL
SELECT s.suspect_id, 
       s.name,
       AVG(i.injury_severity) AS avg_severity,
       MAX(i.injury_severity) AS max_severity,
       COUNT(DISTINCT i.incident_id) AS incident_count
FROM incidents AS i
JOIN suspects s
ON i.suspect_id = s.suspect_id
GROUP BY s.suspect_id -- Need to group by s.name too so it can match
HAVING incident_count > 1
ORDER BY avg_severity DESC
```

```SQL
SELECT s.suspect_id,
       s.name,
       AVG(i.injury_severity) AS avg_severity,
       MAX(i.injury_severity) AS max_severity,
       COUNT(*) AS incident_count 
FROM suspects s 
JOIN incidents i 
ON s.suspect_id = i.suspect_id
GROUP BY s.suspect_id, s.name
HAVING COUNT(*) > 1 
ORDER BY avg_severity DESC;
```

**46) The Location Hunter**

They are hunting in specific zones. Map the grounds by listing the `security_level`, `foot_traffic`, and `incident_count` for each combination, ranked by frequency.

```SQL
-- My incorrect answer (The database is )
SELECT l.security_level,
       l.foot_traffic,
       COUNT(i.incident_id) AS incident_count
FROM locations AS l 
JOIN incidents i ON l.location_id = i.incident_id
GROUP BY l.security_level, l.foot_traffic
ORDER BY incident_count ASC
```

```SQL
-- The correct answer
SELECT l.security_level, 
l.foot_traffic, COUNT(i.incident_id) AS incident_count 
FROM incidents i 
JOIN locations l ON i.location = l.area_name 
GROUP BY l.security_level, l.foot_traffic 
ORDER BY incident_count DESC;
```

*Lesson:* 

- ***The "Hacker" Way:** Join on whatever text matches to get the answer quickly.*
- ***The "Architect" Way:** Use PK/FK to ensure the data is mathematically 100% accurate and lightning-fast.*

**47) The Signature Analysis**

Every predator has a signature. Isolate it for suspect #101 (Marcus Steele). Identify the most common combination of `weapon_used`, `time_period` (Night: 22:00-06:00, else Day), and `victim_type` (Young: < 25, else Older). List the combination and its `pattern_count`, ranked by count.

```SQL
-- My incorrect answer
SELECT i.weapon_used,
    CASE WHEN i.time_of_day >= '22:00:00'
    OR i.time_of_day <= '06:00:00' THEN 'Night' 
    ELSE 'Day' 
  END AS time_period,
    CASE WHEN i.victim_age < 25 THEN 'Young' 
    ELSE 'Older' 
  END AS victim_type,
    COUNT(*) AS pattern_count
FROM incidents AS i
GROUP BY 1, 2, 3
ORDER BY pattern_count DESC;
```

```SQL
-- The correct answer (mostly because I don't understand the challenge correctly)
SELECT weapon_used, 
    CASE WHEN time_of_day >= '22:00' 
    OR time_of_day < '06:00' THEN 'Night'
    ELSE 'Day'
  END AS time_period,
    CASE WHEN victim_age < 25 THEN 'Young' 
    ELSE 'Older'
  END AS victim_type,
  COUNT(*) AS pattern_count
  FROM incidents 
  WHERE suspect_id = 101
  GROUP BY weapon_used, 
  CASE WHEN time_of_day >= '22:00' 
  OR time_of_day < '06:00' THEN 'Night'
  ELSE 'Day' 
  END, 
  CASE WHEN victim_age < 25 THEN 'Young'
  ELSE 'Older'
  END
  ORDER BY pattern_count DESC;
```

**48) The Cooling-Off Period**

Is there a cooling-off period? Check the timeline for suspect #101 by calculating the `incident_count`, `first_incident` ID, and `last_incident` ID to see the full span of his activity.

```SQL
SELECT 
  COUNT(i.incident_id) AS incident_count,
  MIN(i.incident_id) AS first_incident,
  MAX(i.incident_id) AS last_incident
FROM incidents AS i
LEFT JOIN suspects s ON i.suspect_id = s.suspect_id
WHERE s.suspect_id = 101
```

**49) The Accomplice Network**

He might not be working alone. Uncover the network by finding pairs of suspects who share *multiple* location and weapon preferences. List `suspect1`, `suspect2`, and the count of `shared_patterns`, ranked by shared patterns.

```SQL
-- My incorrect answer
SELECT 
    i.suspect_id AS suspect1, 
    s.suspect_id AS suspect2, 
    COUNT(*) AS shared_patterns
FROM incidents AS i
JOIN suspects s 
    ON i.suspect_id = s.suspect_id 
    AND i.location = i.location
GROUP BY suspect1, suspect2
HAVING shared_patterns > 1
ORDER BY shared_patterns DESC;
```

```SQL
-- The correct answer
SELECT 
  i1.suspect_id AS suspect1,
  i2.suspect_id AS suspect2,
  COUNT(*) AS shared_patterns
  FROM incidents i1
  JOIN incidents i2 
  ON i1.location = i2.location 
  AND i1.weapon_used = i2.weapon_used
WHERE i1.suspect_id < i2.suspect_id
GROUP BY i1.suspect_id, i2.suspect_id 
HAVING COUNT(*) > 1
ORDER BY shared_patterns DESC;
```

**50) The Predator Profile**

We have him. Build the final predator profile for Marcus Steele (ID 101). Generate a comprehensive report including `name`, `age`, `arrest_count`, `total_incidents`, `avg_severity`, `max_severity`, `knife_attacks`, `night_attacks`, and `young_victims`.

```SQL
-- My almost correct answer
SELECT s.name, s.age, s.arrest_count, 
       COUNT(DISTINCT i.incident_id) AS total_incidents,
       AVG(i.injury_severity) AS avg_severity,
       MAX(i.injury_severity) AS max_severity,
-- Weapon logic
COUNT(CASE WHEN i.weapon_used = 'Knife' THEN 'knife_attacks'
END) AS knife_attacks,
-- Time logic
COUNT(CASE WHEN i.time_of_day >= '22:00' OR i.time_of_day <= '06:00' THEN 'night_attacks' -- Should use 1 as TRUE
END) AS night_attacks,
-- Age logic
COUNT(CASE WHEN i.victim_age < 25 THEN 'young_victims'
END) AS young_victims

FROM incidents AS i
JOIN suspects s ON i.suspect_id = s.suspect_id
WHERE s.suspect_id = 101
GROUP BY s.name, s.age, s.arrest_count
```

```SQL
-- The correct answer
SELECT s.name, 
       s.age,
       s.arrest_count,
       COUNT(i.incident_id) AS total_incidents,
       AVG(i.injury_severity) AS avg_severity,
       MAX(i.injury_severity) AS max_severity,
       COUNT(CASE WHEN i.weapon_used = 'Knife' THEN 1 END) AS knife_attacks,          COUNT(CASE WHEN i.time_of_day >= '22:00' THEN 1 END) AS night_attacks,         COUNT(CASE WHEN i.victim_age < 25 THEN 1 END) AS young_victims
FROM suspects s
JOIN incidents i ON s.suspect_id = i.suspect_id
WHERE s.suspect_id = 101
GROUP BY s.suspect_id, s.name, s.age, s.arrest_count;
```

# CASE FILE: S06 - Web of Lies

![CASE FILE S06 - Web of Lies](../img/sql-case-files-img/CASE%20FILE%20S06%20-%20Web%20of%20Lies.png)

## Solution

**51) The Network Map**

We've intercepted the dossier. Map the hierarchy by listing the `real_name` of every superior and their subordinate (aliased as `superior` and `subordinate`). Sort by the superior's name.

```SQL
SELECT 
    sup.real_name AS superior, 
    sub.real_name AS subordinate
FROM relationships r
JOIN operatives sup ON r.superior_id = sup.operative_id
JOIN operatives sub ON r.subordinate_id = sub.operative_id
ORDER BY superior
```

**52) The Chain of Command**

Focus on direct orders. Trace the command line by listing the `code_name` of every superior and their subordinate (aliased as `superior` and `subordinate`) linked by a 'Command' relationship. Sort by superior's code name.

```SQL
-- My incorrect answer
SELECT DISTINCT sup.code_name AS superior,
       sub.code_name AS subordinate
FROM relationships AS r
JOIN operatives sup ON r.superior_id = sup.operative_id
JOIN operatives sub ON r.subordinate_id = sub.operative_id 
LEFT JOIN communications c1 ON r.superior_id = c1.comm_id 
LEFT JOIN communications c2 ON r.subordinate_id = c2.comm_id
ORDER BY sup.code_name
```

```SQL
-- The correct answer
SELECT s.code_name AS superior,
       sub.code_name AS subordinate
FROM relationships r 
JOIN operatives s ON r.superior_id = s.operative_id 
JOIN operatives sub ON r.subordinate_id = sub.operative_id 
WHERE r.relationship_type = 'Command' 
ORDER BY s.code_name;
```

**53) The Communication Web**

The wires are hot. Intercept the chatter by listing the `code_name` of the sender and receiver (aliased as `sender` and `receiver`) along with the `message_type`. Sort by the sender's code name.

```SQL
SELECT s.code_name AS sender,
       r.code_name AS receiver,
       c.message_type
FROM communications AS c
JOIN operatives s ON c.sender_id = s.operative_id
JOIN operatives r ON c.receiver_id = r.operative_id
ORDER BY s.code_name
```

**54) The Recruitment Chain**

We need to find who is bringing them in. Expose the recruitment pipeline by listing the `code_name` of any 'Recruiter' and their 'Scout' or 'Coordinator' subordinate (aliased as `recruiter` and `recruit`), along with the recruit's `role`. Sort by the recruiter's code name.

```SQL
-- r1 = recruiter | r2 = recruit
SELECT 
    r1.code_name AS recruiter,
    r2.code_name AS recruit,
    r2.role AS role
FROM relationships AS r
JOIN operatives AS r1 ON r.superior_id = r1.operative_id
JOIN operatives AS r2 ON r.subordinate_id = r2.operative_id
WHERE r1.role = 'Recruiter'
  AND r2.role IN ('Scout', 'Coordinator')
ORDER BY recruiter;
```

**55) The Enforcement Network**

They work in teams. Identify the muscle by finding pairs of 'Enforcer' operatives linked by a 'Partnership'. List their code names as `enforcer1` and `enforcer2`. Sort by `enforcer1` code name.

```SQL
SELECT sup.code_name AS enforcer1,
       sub.code_name AS enforcer2
FROM relationships AS r
JOIN operatives sup ON r.superior_id = sup.operative_id
JOIN operatives sub ON r.subordinate_id = sub.operative_id
WHERE (sup.role= 'Enforcer' OR sub.role = 'Enforcer')
  AND r.relationship_type = 'Partnership'  
ORDER BY enforcer1;
```

**56) The Compromised Assets**

Raven has turned. We need a damage assessment. List the distinct `code_name` and `role` of any operative who has a direct relationship with 'Raven'. Sort by `code_name`.

```SQL
SELECT DISTINCT o.code_name, o.role
FROM operatives o
JOIN relationships r ON o.operative_id = r.superior_id 
OR o.operative_id = r.subordinate_id
WHERE (
    r.superior_id = (SELECT operative_id FROM operatives WHERE code_name = 'Raven')
OR 
r.subordinate_id = (SELECT operative_id FROM operatives WHERE code_name = 'Raven')
)
AND o.code_name != 'Raven'
ORDER BY o.code_name;
```

**57) The Communication Frequency**

High volume means high priority. Analyze traffic volume by counting the messages exchanged between each pair of operatives. List the `sender`, `receiver`, and `message_count`, ranked by frequency.

```SQL
SELECT s.code_name AS sender,
       r.code_name AS receiver,
       COUNT(c.comm_id) AS message_count
FROM communications AS c
JOIN operatives s ON s.operative_id = c.sender_id
JOIN operatives r ON r.operative_id = c.receiver_id
GROUP BY sender, receiver
ORDER BY message_count DESC
```

**58) The Operational Timeline**

When did this start? Reconstruct the timeline by listing the `superior` and `subordinate` code names, `relationship_type`, and `established_date` for all connections, sorted chronologically.

```SQL
SELECT sup.code_name AS superior,
       sub.code_name AS subordinate,
       r.relationship_type,
       r.established_date
FROM relationships AS r
JOIN operatives sup ON r.superior_id = sup.operative_id
JOIN operatives sub ON r.subordinate_id = sub.operative_id
ORDER BY r.established_date ASC
```

**59) The Network Depth**

The Architect uses layers of protection. Peel them back. Identify the operatives two steps removed from 'The Architect'. List the hierarchy as `top_leader`, `middle_manager`, and `subordinate`, sorted by `middle_manager`.

```SQL
SELECT 'The Architect' AS top_manager,
       m_op.code_name AS middle_manager,
       s_op.code_name AS subordinate
FROM operatives AS a_op
-- 1st layer
JOIN relationships r1 ON a_op.operative_id = r1.superior_id 
JOIN operatives m_op ON r1.subordinate_id = m_op.operative_id
-- 2nd layer 
JOIN relationships r2 ON m_op.operative_id = r2.superior_id 
JOIN operatives s_op ON r2.subordinate_id = s_op.operative_id
WHERE a_op.code_name = 'The Architect'
ORDER BY middle_manager
```

**60) The Complete Network**

This is the takedown. Map the full network by generating a dossier for each operative including `code_name`, `real_name`, `role`, `status`, `total_connections`, and `total_communications`. Rank by connections, then communications.

```SQL
SELECT o.operative_id,
       o.code_name,
       o.real_name,
       o.role,
       o.status,
(SELECT COUNT(*)
     FROM relationships r
     WHERE r.superior_id = o.operative_id
     OR r.subordinate_id = o.operative_id) AS total_connections,
(SELECT COUNT(*)
     FROM communications c
     WHERE c.sender_id = o.operative_id
     OR c.receiver_id = o.operative_id) AS total_communications
FROM operatives AS o
ORDER BY total_connections DESC, total_communications DESC
```

*Official solution:*

```SQL
SELECT DISTINCT o.operative_id, o.code_name, o.real_name, o.role, o.status, 

COUNT(DISTINCT r1.relationship_id) + COUNT(DISTINCT r2.relationship_id) AS total_connections, 

COUNT(DISTINCT c1.comm_id) + COUNT(DISTINCT c2.comm_id) AS total_communications 

FROM operatives o 
LEFT JOIN relationships r1 ON o.operative_id = r1.superior_id 
LEFT JOIN relationships r2 ON o.operative_id = r2.subordinate_id 
LEFT JOIN communications c1 ON o.operative_id = c1.sender_id LEFT JOIN communications c2 ON o.operative_id = c2.receiver_id 
GROUP BY o.operative_id, o.code_name, o.real_name, o.role, o.status 
ORDER BY total_connections DESC, total_communications DESC;
```

*Alternative solution using CTE:*

```SQL
WITH ConnectionCounts AS (
    SELECT operative_id, COUNT(*) as c_count
    FROM (
        SELECT superior_id AS operative_id FROM relationships
        UNION ALL
        SELECT subordinate_id AS operative_id FROM relationships
    )
    GROUP BY operative_id
),
CommCounts AS (
    SELECT operative_id, COUNT(*) as m_count
    FROM (
        SELECT sender_id AS operative_id FROM communications
        UNION ALL
        SELECT receiver_id AS operative_id FROM communications
    )
    GROUP BY operative_id
)
SELECT 
    o.code_name, o.real_name, o.role, o.status,
    -- COALESCE to turn "NULL" into "0" for operatives with no activity.
    COALESCE(cc.c_count, 0) AS total_connections,
    COALESCE(cm.m_count, 0) AS total_communications
FROM operatives o
LEFT JOIN ConnectionCounts cc ON o.operative_id = cc.operative_id
LEFT JOIN CommCounts cm ON o.operative_id = cm.operative_id
ORDER BY total_connections DESC, total_communications DESC;
```

# CASE FILE: S07 - Ghost Accounts

![CASE FILE S07 - Ghost Accounts](../img/sql-case-files-img/CASE%20FILE%20S07%20-%20Ghost%20Accounts.png)

## Solution

**61) The Risk Assessment**

Financial Crimes needs these categorized. Triage the transfers by listing the `transaction_id` and `amount`. Categorize each as 'High Risk' (>$100k), 'Medium Risk' ($50k-$100k), or 'Low Risk' (<$50k), aliased as `risk_category`.

```SQL
SELECT  t.transaction_id, t.amount,
  CASE
  WHEN t.amount > 100000 THEN 'High Risk'
  WHEN t.amount >= 50000 THEN 'Medium Risk'
  ELSE 'Low risk'
END AS 'risk_category'
FROM transactions AS t
```

**62) The Account Classification**

Don't let them slip through. Classify the accounts by listing `account_name`, `account_type`, and `owner_name`. Assign a `monitoring_level`: 'Maximum' for Shell/Offshore, 'High' for Business with 'Unknown' owners, else 'Standard'.

```SQL
SELECT a.account_name, 
       a.account_type,
       a.owner_name,
CASE
  WHEN a.account_type IN ('Shell', 'Offshore') THEN 'Maximum'
  WHEN a.account_type = 'Business' AND a.owner_name = 'Unknow' THEN 'High'
  ELSE 'Standard'
END AS monitoring_level
FROM 
  accounts AS a
```

**63) The High Value Transfer**

We need to see the flow. Follow it by listing `transaction_id`, `amount`, `creation_date`, and `transaction_date`. Categorize by value: 'High Value' (>$100k), 'Medium Value' (>$50k), else 'Low Value', aliased as `risk_level`.

```SQL
SELECT t.transaction_id,
       t.amount,
       a.creation_date,
       t.transaction_date,
CASE
  WHEN t.amount > 100000 THEN 'High Value'
  WHEN t.amount > 50000 THEN 'Medium Value'
  ELSE 'Low Value'
END AS risk_level

FROM transactions AS t
JOIN accounts a
ON t.account_from = a.account_id
```

**64) The Layering Detection**

Find the hubs moving money in circles. For each account, list the `account_name` and `transaction_count`. Categorize `activity_level`: 'Layering Hub' (>5 transactions), 'Active' (3-5), else 'Normal'.

```SQL
-- My incorrect answer (why im I overcomplicating this T_T)
WITH AccountStats AS (
  SELECT a.account_name,
    COUNT(t.transaction_id) AS transaction_count
  FROM accounts AS a
  LEFT JOIN transactions t
  ON t.account_from = a.account_id
  GROUP BY a.account_name
)
SELECT
  account_name,
  transaction_count,
CASE
  WHEN transaction_count > 5 THEN 'Layering Hub'
  WHEN transaction_count >= 3 THEN 'Active'
ELSE 'Normal'
END AS activity_level
FROM AccountStats
```

```SQL
-- The correct answer
SELECT a.account_name, 
       COUNT(t.transaction_id) AS transaction_count,
       CASE 
         WHEN COUNT(t.transaction_id) > 5 THEN 'Layering Hub'
         WHEN COUNT(t.transaction_id) >= 3 THEN 'Active'
         ELSE 'Normal'
         END AS activity_level 
FROM accounts a 
LEFT JOIN transactions t 
ON a.account_id = t.account_from 
OR a.account_id = t.account_to 
GROUP BY a.account_id, a.account_name;
```

**65) The Value Concentration**

Watch for concentration. Spot the bottlenecks by listing `account_name`, `total_volume`, and `percentage` of global volume. Flag `concentration_level`: 'High Concentration' (>20%), 'Medium Concentration' (>10%), else 'Low Concentration'. Sort by total volume descending.

```SQL
-- My incorrect answer
SELECT a.account_name,
       COUNT(t.transaction_id) * SUM(t.amount) AS total_volume,
       COUNT(t.transaction_id) / SUM(total_volume) * 100 AS percentage,
CASE
  WHEN percentage > 20 THEN 'High Concentration'
  WHEN percentage > 10 THEN 'Medium Concentration'
ELSE 'Low'
END AS concentration_level
FROM accounts AS a
LEFT JOIN transactions t
ON a.account_id = t.account_from
OR a.account_id = t.account_to
GROUP BY a.account_name
ORDER BY total_volume ASC
```

```SQL
--  The correct answer
SELECT a.account_name,
       SUM(t.amount) AS total_volume,
       ROUND((SUM(t.amount) * 100.0 / (SELECT SUM(amount)
       FROM transactions)), 2) AS percentage,
       CASE 
         WHEN (SUM(t.amount) * 100.0 / (SELECT SUM(amount) FROM transactions)) > 20 THEN 'High Concentration' 
         WHEN (SUM(t.amount) * 100.0 / (SELECT SUM(amount) FROM transactions)) > 10 THEN 'Medium Concentration' 
       ELSE 'Low Concentration'
       END AS concentration_level 
FROM accounts a 
JOIN transactions t 
ON a.account_id = t.account_from 
GROUP BY a.account_id, a.account_name 
ORDER BY total_volume DESC;
```

``` SQL
-- Bonus: the CTE way for better performance and readablitiy (Gemini3)
WITH GlobalData AS (
    -- Calculate the grand total once so we don't repeat the subquery
    SELECT SUM(amount) AS grand_total FROM transactions
),
AccountTotals AS (
    -- Calculate individual account volumes
    SELECT 
        a.account_name,
        SUM(t.amount) AS total_volume
    FROM accounts AS a
    JOIN transactions AS t ON a.account_id = t.account_from
    GROUP BY a.account_id, a.account_name
)
SELECT 
    at.account_name,
    at.total_volume,
    -- Simple division using the values from our CTEs
    ROUND((at.total_volume * 100.0 / gd.grand_total), 2) AS percentage,
    CASE 
        WHEN (at.total_volume * 100.0 / gd.grand_total) > 20 THEN 'High Concentration'
        WHEN (at.total_volume * 100.0 / gd.grand_total) > 10 THEN 'Medium Concentration'
        ELSE 'Low Concentration'
    END AS concentration_level
FROM AccountTotals AS at, GlobalData AS gd
ORDER BY at.total_volume DESC;
```

**66) The Shell Company Detector**

Build a detector. Profile the shells by listing `company_name`, `registration_date`, `address`, and `is_shell`. Calculate `shell_probability`: 'Confirmed Shell' (if known), 'Highly Suspicious' (PO Box & New [> 2025-01-01]), 'Suspicious' (PO Box OR New), else 'Legitimate'.

```SQL
SELECT c.company_name,
       c.registration_date,
       c.address,
       c.is_shell,
CASE
  WHEN c.is_shell = 1 THEN 'Confirmed Shell'
  WHEN c.address LIKE '%PO Box%' AND c.registration_date > '2025-01-01' THEN 'Highly Suspicious'
  WHEN c.address LIKE '%PO Box%' OR c.registration_date > '2025-01-01' THEN 'Suspicious'
  ELSE 'Legitimate'
END AS shell_probability
FROM companies AS c
```

**67) The Transaction Chain Analysis**

Find the rapid-fire sequences. Trace the chains by listing `transaction_id`, `amount`, `account_to`, and the `next_transaction` ID. Categorize `chain_status`: 'Same Day Chain' (same date), 'Possible Chain' (any next txn), else 'Terminal'. Sort by transaction date.

```SQL
-- My incorrect answer (even with AI assist)
SELECT t.transaction_id,
       t.amount,
       t.account_to,
LEAD (t.transaction_id)
OVER (
    PARTITION BY t.account_to
    ORDER BY t.transaction_date, t.transaction_id
) AS next_transaction,
CASE
  WHEN LEAD (t.transaction_id)
       OVER (
           PARTITION BY t.account_to
           ORDER BY t.transaction_date, t.transaction_id
            ) = t.transaction_date
  THEN 'Same Day Chain'
  WHEN LEAD (t.transaction_id)
       OVER (
           PARTITION BY t.account_to
           ORDER BY t.transaction_date
            ) IS NOT NULL
  THEN 'Possible Chain'
ELSE 'Terminal'
END AS chain_status
FROM transactions AS t
ORDER BY t.transaction_date DESC
```

```SQL
-- The correct answer
SELECT t1.transaction_id,
       t1.amount,
       t1.account_to,
       t2.transaction_id AS next_transaction,
       CASE WHEN t2.transaction_id IS NOT NULL 
       AND t1.transaction_date = t2.transaction_date THEN 'Same Day Chain' 
       WHEN t2.transaction_id IS NOT NULL THEN 'Possible Chain' 
       ELSE 'Terminal' 
       END AS chain_status
FROM transactions t1 
LEFT JOIN transactions t2 
ON t1.account_to = t2.account_from 
AND t2.transaction_date >= t1.transaction_date 
ORDER BY t1.transaction_date;
```

**68) The Round Number Detector**

Criminals are lazy. Catch them by listing `transaction_id` and `amount`. Categorize `amount_pattern`: 'Multiple of $10K', 'Multiple of $5K', 'Multiple of $1K', else 'Irregular Amount'. Sort by amount descending.

```SQL
SELECT t.transaction_id,
       t.amount,
CASE
  WHEN t.amount % 10000 = 0 THEN 'Multiple of $10k'
  WHEN t.amount % 5000 = 0 THEN 'Multiple of $5k'
  WHEN t.amount % 1000 = 0 THEN 'Multiple of $1k'
ELSE 'Irregular Amount'
END AS amount_pattern
FROM transactions AS t
ORDER BY t.amount DESC
```

**69) The Velocity Analysis**

How fast is the cash spinning? Measure the velocity by listing `account_from`, `transaction_date`, and `prev_date`. Categorize `velocity_category`: 'First Transaction', 'Same Day' (if dates match), else 'Different Day'. Sort by account and transaction date.

```SQL
-- My incorrect answer
SELECT t1.account_from,
       t1.transaction_date,
       t2.transaction_date AS prev_date,
  CASE
    WHEN t1.transaction_date = MIN(t2.transaction_date) THEN 'First Transaction'
    WHEN t1.transaction_date = t2.transaction_date THEN 'Same Day'
  ELSE 'Different Day'
END AS velocity_category
FROM transactions t1 
LEFT JOIN transactions t2 
ON t1.account_to = t2.account_from 
AND t2.transaction_date <= t1.transaction_date
GROUP BY t1.account_from, t1.transaction_date
ORDER BY t1.account_from, t1.transaction_date
```

```SQL
-- The correct answer (Using window function LAG())
SELECT account_from,
       transaction_date,
       LAG(transaction_date) OVER (
         PARTITION BY account_from 
           ORDER BY transaction_date) AS prev_date,
           CASE WHEN LAG(transaction_date) OVER (
           PARTITION BY account_from 
           ORDER BY transaction_date) IS NULL THEN 'First Transaction' 
           WHEN transaction_date = LAG(transaction_date) 
           OVER (PARTITION BY account_from 
           ORDER BY transaction_date) THEN 'Same Day' 
         ELSE 'Different Day' 
       END AS velocity_category 
FROM transactions 
ORDER BY account_from, transaction_date;
```

**70) The Laundering Score**

We need a prioritized hit list. Calculate the threat score in a master report listing `transaction_id`, `amount`, `from_type`, `to_type`. Calculate a weighted `risk_score` based on amount, account types, and round numbers. Assign a `risk_category` (Critical/High/Medium/Low). Sort by risk score descending.

```SQL
-- My AI assisted incorrect answer (The challenge is too short and im too dumb)
WITH TransactionDetails AS (
     SELECT
        t.transaction_id,
        t.amount,
        a_from.account_type AS from_type,
        a_to.account_type AS to_type,
        -- Round number check
      CASE WHEN t.amount % 1000 = 0 THEN 1 
      ELSE 0 
    END AS is_round
FROM transactions AS t
JOIN accounts a_from
ON t.account_from = a_from.account_id
JOIN accounts a_to
ON t.account_to = a_to.account_id
),

ScoredTransactions AS (
      SELECT
          *,
          (CASE WHEN amount > 50000 THEN 50 ELSE 0 END +
           CASE WHEN is_round = 1 THEN 30 ELSE 0 END +
           CASE WHEN from_type = 'Personal' AND to_type = 'Coorporate' THEN 20 ELSE 0 END
           ) AS risk_score
FROM TransactionDetails
)

SELECT
     transaction_id,
     amount,
     from_type,
     to_type,
     risk_score,
     CASE
         WHEN risk_score >= 80 THEN 'Critical'
         WHEN risk_score >= 50 THEN 'High'
         WHEN risk_score >= 20 THEN 'Medium'
         ELSE 'Low'
     END AS risk_category
FROM ScoredTransactions
ORDER BY risk_score DESC
```

```SQL
-- The correct answer (WTF? I think this could be refactor)
SELECT t.transaction_id, 
       t.amount, 
       a_from.account_type AS from_type, 
       a_to.account_type AS to_type, 
       (CASE WHEN t.amount > 100000 THEN 3 
             WHEN t.amount > 50000 THEN 2 
             ELSE 1 END + 
             CASE 
             WHEN a_from.account_type = 'Shell' THEN 3 
             WHEN a_from.account_type = 'Offshore' THEN 2 
             ELSE 0 END + 
             CASE 
             WHEN a_to.account_type = 'Offshore' THEN 3 
             WHEN a_to.account_type = 'Shell' THEN 2 
             ELSE 0 END + 
             CASE 
             WHEN t.amount % 10000 = 0 THEN 2 
             ELSE 0 END) AS risk_score, 
             CASE 
             WHEN (CASE WHEN t.amount > 100000 THEN 3 
             WHEN t.amount > 50000 THEN 2 
             ELSE 1 END + 
             CASE 
             WHEN a_from.account_type = 'Shell' THEN 3 
             WHEN a_from.account_type = 'Offshore' THEN 2 
             ELSE 0 END + 
             CASE 
             WHEN a_to.account_type = 'Offshore' THEN 3 
             WHEN a_to.account_type = 'Shell' THEN 2 
             ELSE 0 END + 
             CASE 
             WHEN t.amount % 10000 = 0 THEN 2 
             ELSE 0 END) >= 8 THEN 'Critical Risk' 
             WHEN (CASE WHEN t.amount > 100000 THEN 3 
             WHEN t.amount > 50000 THEN 2 
             ELSE 1 END + 
             CASE 
             WHEN a_from.account_type = 'Shell' THEN 3 
             WHEN a_from.account_type = 'Offshore' THEN 2 
             ELSE 0 END + 
             CASE 
             WHEN a_to.account_type = 'Offshore' THEN 3 
             WHEN a_to.account_type = 'Shell' THEN 2 
             ELSE 0 END + 
             CASE 
             WHEN t.amount % 10000 = 0 THEN 2 
             ELSE 0 END) >= 5 THEN 'High Risk' 
             WHEN (CASE WHEN t.amount > 100000 THEN 3 
             WHEN t.amount > 50000 THEN 2 
             ELSE 1 END + 
             CASE 
             WHEN a_from.account_type = 'Shell' 
             THEN 3 WHEN a_from.account_type = 'Offshore' THEN 2 
             ELSE 0 END + 
             CASE 
             WHEN a_to.account_type = 'Offshore' THEN 3 
             WHEN a_to.account_type = 'Shell' THEN 2 
             ELSE 0 END + 
             CASE WHEN t.amount % 10000 = 0 THEN 2 
             ELSE 0 END) >= 3 THEN 'Medium Risk' 
             ELSE 'Low Risk' 
             END AS risk_category 
FROM transactions t 
JOIN accounts a_from 
ON t.account_from = a_from.account_id 
JOIN accounts a_to 
ON t.account_to = a_to.account_id 
ORDER BY risk_score DESC;
```

*Bonus: Optimized version refactored by Gemini3*

```SQL
WITH BaseRisk AS (
    -- Step 1: Calculate the raw score once
    SELECT 
        t.transaction_id, 
        t.amount, 
        a_from.account_type AS from_type, 
        a_to.account_type AS to_type,
        (
          -- Amount Weight
          CASE WHEN t.amount > 100000 THEN 3 
               WHEN t.amount > 50000 THEN 2 ELSE 1 END + 
          -- Sender Weight
          CASE WHEN a_from.account_type = 'Shell' THEN 3 
               WHEN a_from.account_type = 'Offshore' THEN 2 ELSE 0 END + 
          -- Receiver Weight
          CASE WHEN a_to.account_type = 'Offshore' THEN 3 
               WHEN a_to.account_type = 'Shell' THEN 2 ELSE 0 END + 
          -- Round Number Weight
          CASE WHEN t.amount % 10000 = 0 THEN 2 ELSE 0 END
        ) AS risk_score
    FROM transactions t
    JOIN accounts a_from ON t.account_from = a_from.account_id
    JOIN accounts a_to ON t.account_to = a_to.account_id
)

-- Step 2: Reference the calculated score to assign categories
SELECT 
    *,
    CASE 
        WHEN risk_score >= 8 THEN 'Critical Risk'
        WHEN risk_score >= 5 THEN 'High Risk'
        WHEN risk_score >= 3 THEN 'Medium Risk'
        ELSE 'Low Risk'
    END AS risk_category
FROM BaseRisk
ORDER BY risk_score DESC;
```

# CASE FILE: S08 - Public Doubts

![CASE FILE S08 - Public Doubts](../img/sql-case-files-img/CASE%20FILE%20S08%20-%20Public%20Doubts.png)

## Solution

**71) Above Average Contracts**

Tip says contracts are padded. Find the ones blowing the average. List the `contract_id`, `vendor_name`, and `contract_value` for any contract that exceeds the city-wide average contract value.

```SQL
SELECT 
    contract_id, 
    vendor_name, 
    contract_value
FROM contracts
WHERE contract_value > (SELECT AVG(contract_value)FROM contracts)
```

**72) The Prolific Approver**

Who is rubber-stamping deals? Spot the excessive approvers by listing the `approving_official` and their `contracts_approved` count for anyone approving more deals than the average official.

```SQL
-- CTE
WITH OfficialCounts AS (
    SELECT 
        approving_official, 
        COUNT(contract_id) AS contracts_approved
    FROM contracts
    GROUP BY approving_official
)
SELECT approving_official, contracts_approved
FROM OfficialCounts
WHERE contracts_approved > (SELECT AVG(contracts_approved) FROM OfficialCounts)
```

**73) The Department Spending Spree**

Audit the departments. Who is spending double the average? List the `department` and `total_spending` for any department spending more than double the average departmental budget.

```SQL
WITH DeptTotals AS (
    SELECT 
        c.department, 
        SUM(c.contract_value) AS total_spending
    FROM contracts AS c
    GROUP BY c.department
)
SELECT 
    department, 
    total_spending
FROM DeptTotals
WHERE total_spending > (SELECT AVG(total_spending) * 2 FROM DeptTotals)
```

**74) The Vendor Favoritism**

Nepotism check. Match official names to vendor owners. Expose it by listing `contract_id`, `vendor_name`, `approving_official`, and `owner_name` where the vendor's owner shares a **partial name** (e.g. last name) with the official.

```SQL
-- My incorrect answer
SELECT c.contract_id,
       c.vendor_name,
       c.approving_official,
       v.owner_name
FROM contracts AS c
JOIN vendors v ON c.vendor_name = v.company_name
WHERE LOWER(c.approving_official) LIKE LOWER('%' || v.owner_name || '%') 
OR LOWER(v.owner_name) LIKE LOWER('%' || c.approving_official || '%')
```

```SQL
-- The correct answer
SELECT c.contract_id,
       c.vendor_name,
       c.approving_official,
       v.owner_name 
FROM contracts c 
JOIN vendors v 
ON c.vendor_name = v.company_name 
WHERE EXISTS (SELECT 1 FROM vendors v2 WHERE v2.company_name = c.vendor_name AND (v2.owner_name LIKE '%Hayes%' OR v2.owner_name LIKE '%Foster%'));
```

**75) The Payment Timing**

Check the speed. Who is getting paid before the ink is dry? Flag the fast-tracking by listing `contract_id`, `vendor_name`, `approval_date`, and `payment_date`. Calculate `days_to_payment` for any deal paid out faster than the **global** average processing time.

```SQL
-- My incorrect answer
SELECT c.contract_id,
       c.vendor_name,
       c.approval_date,
       p.payment_date,
       (p.payment_date - c.approval_date) AS days_to_payment
FROM contracts AS c
JOIN payments p ON c.contract_id = p.contract_id
WHERE (p.payment_date - c.approval_date) < (
    SELECT AVG(p2.payment_date - c2.approval_date)
    FROM contracts c2
    JOIN payments p2 ON c2.contract_id = p2.contract_id
)
```

```SQL
-- The correct answer
SELECT c.contract_id, 
       c.vendor_name,
       c.approval_date,
       p.payment_date,
       JULIANDAY(p.payment_date) - JULIANDAY(c.approval_date) AS days_to_payment 
FROM contracts c 
JOIN payments p 
ON c.contract_id = p.contract_id 
WHERE JULIANDAY(p.payment_date) - JULIANDAY(c.approval_date) < (SELECT AVG(JULIANDAY(p2.payment_date) - JULIANDAY(c2.approval_date)) 
FROM contracts c2 
JOIN payments p2 ON c2.contract_id = p2.contract_id);
```

**76) The Salary Mismatch**

Salary mismatch. Why is a mid-level manager signing million-dollar checks? Check the authority limits by listing `contract_id`, `vendor_name`, `contract_value`, `approving_official`, and `salary` where the contract value is more than 10x the official's annual salary.

```SQL
SELECT c.contract_id,
       c.vendor_name,
       c.contract_value,
       c.approving_official,
       o.salary
FROM contracts AS c
JOIN officials o 
ON c.department = o.department
WHERE c.contract_value > o.salary * 12
```

**77) The Vendor Concentration**

Favoritism. Who is funneling half their budget to a single friend? Detect vendor bias by identifying if a single vendor gets >30% of an official's total approval budget. List `approving_official`, `vendor_name`, `vendor_total`, `official_total`, and `percentage`. Sort by percentage descending.

```SQL
-- My incorrect answer
WITH BudgetSummary AS (
SELECT
     approving_official,
     vendor_name,
     SUM(contract_value) AS vendor_total,
     SUM(SUM(contract_value)) OVER(PARTITION BY approving_official) AS official_total
FROM contracts
GROUP BY approving_official, vendor_name
)
SELECT 
     DISTINCT approving_official,
     vendor_name,
     vendor_total,
     official_total,
     (vendor_total * 100.0 / official_total) AS percentage
FROM BudgetSummary
WHERE (vendor_total * 100.0 / official_total) > 30
ORDER BY percentage DESC
```

```SQL
-- The correct answer
SELECT approving_official, 
       vendor_name,
       vendor_total,
       official_total,
       ROUND((vendor_total * 100.0 / official_total),2) AS percentage 
FROM (SELECT c1.approving_official,
             c1.vendor_name,
             SUM(c1.contract_value) AS vendor_total,
    (SELECT SUM(c2.contract_value) 
    FROM contracts c2 
    WHERE c2.approving_official = c1.approving_official) AS official_total FROM contracts c1 
GROUP BY c1.approving_official, c1.vendor_name) 
WHERE (vendor_total * 100.0 / official_total) > 30 
ORDER BY percentage DESC;
```

**78) The New Vendor Advantage**

Pop-up vendors. Big contracts to companies born yesterday? Investigate these pop-ups by listing `contract_id`, `vendor_name`, `contract_value`, `approval_date`, and `registration_date`. Calculate `days_since_registration` for vendors less than a year old at the time of approval.

```SQL
-- My incorrect answer
SELECT c.contract_id,
       c.vendor_name,
       c.contract_value,
       c.approval_date,
       v.registration_date,
       (c.approval_date - v.registration_date) AS days_since_registration
FROM contracts c
JOIN vendors v
ON c.vendor_name = v.company_name
WHERE (c.approval_date - v.registration_date) < 365
AND (c.approval_date - v.registration_date) >= 0
ORDER BY days_since_registration ASC
```

```SQL
-- The correct answer
SELECT c.contract_id,
       c.vendor_name,
       c.contract_value,
       c.approval_date,
       v.registration_date,
       JULIANDAY(c.approval_date) - JULIANDAY(v.registration_date) AS days_since_registration 
FROM contracts c 
JOIN vendors v 
ON c.vendor_name = v.company_name
WHERE JULIANDAY(c.approval_date) - JULIANDAY(v.registration_date) < 365;
```

**79) The Approval Authority**

Authority breach. Why is IT signing for Public Works? Audit the jurisdiction by listing `contract_id`, `vendor_name`, `contract_value`, `contract_dept`, `approving_official`, `official_dept`, and `position` for approvals outside the official's department or exceeding 2x their position's **average contract value**.

```SQL
WITH PositionAvg AS (
SELECT o.position,
       AVG(c.contract_value) AS avg_pos_val
FROM officials AS o
JOIN contracts c
ON o.name = c.approving_official
GROUP BY o.position
)

SELECT c.contract_id, 
       c.vendor_name,
       c.contract_value,
       c.department AS contract_dept,
       c.approving_official,
       o.department AS official_dept,
       o.position
FROM contracts c
JOIN officials o
ON c.approving_official = o.name
JOIN PositionAvg pa
ON o.position = pa.position
WHERE contract_dept != official_dept
OR c.contract_value > (pa.avg_pos_val * 2)
```

**80) The Corruption Network**

Build the RICO case. Map the full network of corruption. Generate a master report listing `contract_id`, `vendor_name`, `contract_value`, `approving_official`, and `owner_name`. Flag suspicious activity: `relationship_flag` (Name Match), `price_flag` (Overpriced > 2x avg), and `vendor_age_flag` (New Vendor < 1 yr). Include `official_contract_count`. Sort by contract value descending.

```SQL
-- My incorrect answer
WITH GlobalMetrics AS (
     SELECT AVG(contract_value) AS global_avg FROM contracts
),
OfficialStats AS (
      SELECT approving_official, COUNT(contract_id) AS official_contract_count
FROM contracts
GROUP BY approving_official
),

AuditBase AS (
SELECT c.contract_id,
       c.vendor_name,
       c.contract_value,
       c.approving_official,
       v.owner_name,
       os.official_contract_count,
       CASE
       WHEN c.approving_official = v.owner_name THEN 'Name Match'
ELSE NULL
END AS relationship_flag,
       CASE
       WHEN c.contract_value > (SELECT global_avg * 2 FROM GlobalMetrics) THEN 'Overpriced' 
ELSE NULL
END AS price_flag,
       CASE 
       WHEN (JULIANDAY(c.approval_date) - JULIANDAY(v.registration_date)) < 365 THEN 'New Vendor' ELSE NULL END AS vendor_age_flag
FROM contracts AS c
JOIN vendors v 
ON c.vendor_name = v.company_name
JOIN OfficialStats os 
ON c.approving_official = os.approving_official 
)
SELECT *
FROM AuditBase
ORDER BY contract_value DESC
```

```SQL
-- The correct answer
SELECT c.contract_id, 
       c.vendor_name, 
       c.contract_value, 
       c.approving_official, 
       v.owner_name, 
       CASE 
       WHEN v.owner_name 
       LIKE '%' || SUBSTR(c.approving_official, INSTR(c.approving_official, ' ') + 1) || '%' THEN 'Name Match' 
       ELSE 'No Match' 
       END AS relationship_flag, 
       CASE 
       WHEN c.contract_value > (SELECT AVG(contract_value) * 2 FROM contracts) THEN 'Overpriced' 
       ELSE 'Normal' 
       END AS price_flag, 
       CASE 
       WHEN JULIANDAY(c.approval_date) - JULIANDAY(v.registration_date) < 365 THEN 'New Vendor' 
       ELSE 'Established' 
       END AS vendor_age_flag, 

(SELECT COUNT(*) 
FROM contracts c2 
WHERE c2.approving_official = c.approving_official) AS official_contract_count 

FROM contracts c 
JOIN vendors v 
ON c.vendor_name = v.company_name 
WHERE (v.owner_name LIKE '%Hayes%' OR v.owner_name LIKE '%Foster%') 
OR c.contract_value > (SELECT AVG(contract_value) * 2 FROM contracts) 
OR JULIANDAY(c.approval_date) - JULIANDAY(v.registration_date) < 365 
ORDER BY c.contract_value DESC;
```

# CASE FILE: S09 - Insider Threat

![CASE FILE S09 - Insider Threat@2x.png](../img/sql-case-files-img/CASE%20FILE%20S09%20-%20Insider%20Threat@2x.png)

## Solution

**81) The Access Timeline**

Sequence the logs. I need to see the order of operations for every suspect. Number them by listing `employee_id`, `file_path`, and `access_time`, assigning an `access_sequence` number ordered chronologically per employee.

```SQL
SELECT a.employee_id,
       a.file_path,
       a.access_time,
       ROW_NUMBER() 
       OVER (PARTITION BY a.employee_id
              ORDER BY access_time ASC) AS access_sequence
FROM access_logs AS a
ORDER BY employee_id, access_time ASC
```

**82) The Previous Access**

Context is everything. Look backward by listing `employee_id`, `file_path`, and `access_time`. Include the `previous_file` and `previous_time` for each entry within the employee's history.

```SQL
SELECT employee_id,
       file_path,
       access_time,
  LAG (file_path, 1) 
  OVER (PARTITION BY employee_id
  ORDER BY access_time ASC
  ) AS previous_file,
  LAG (access_time, 1) 
  OVER (PARTITION BY employee_id
  ORDER BY access_time ASC
  ) AS previous_time
FROM access_logs
ORDER BY employee_id, access_time
```

**83) The Next Target**

Trace the path. Look forward by listing `employee_id`, `file_path`, and `access_time`. Include the `next_file` and `next_time` for each entry within the employee's history.

```SQL
SELECT employee_id,
       file_path,
       access_time,
LEAD(file_path, 1) OVER (
        PARTITION BY employee_id 
        ORDER BY access_time ASC
    ) AS next_file,
LEAD(access_time, 1) OVER (
        PARTITION BY employee_id 
        ORDER BY access_time ASC
    ) AS next_time
FROM access_logs
```

**84) The Access Gaps**

Rapid access means bulk theft. Check the gaps by listing `employee_id`, `file_path`, and `access_time`. Calculate `previous_time` and the `minutes_diff` since the last access for that employee.

```SQL
-- CTE for previous_time
WITH prevTime AS (
       SELECT
       employee_id,
       file_path,
       access_time,
       LAG(access_time, 1) OVER(
       PARTITION BY employee_id
       ORDER BY access_time ASC
       ) AS previous_time
FROM access_logs
)
SELECT employee_id,
       file_path,
       access_time,
       previous_time,
       ROUND((JULIANDAY(access_time) - JULIANDAY(previous_time)) * 1440, 2) AS minutes_diff
FROM prevTime
```

**85) The Ranking System**

Who is the heaviest user? Rank the users by listing `employee_id` and `total_accesses`. Assign both an `access_rank` and `dense_rank` based on total activity, from highest to lowest.

```SQL
SELECT employee_id,
       COUNT(access_time) AS total_accesses,
       RANK() OVER(
       ORDER BY COUNT(access_time) DESC
       ) AS access_rank,
       DENSE_RANK() OVER(
       ORDER BY COUNT(access_time) DESC
       ) AS dense_rank
FROM access_logs
GROUP BY employee_id
ORDER BY total_accesses DESC
```

**86) The Running Total**

If that line goes vertical, we have a breach. Watch the running volume by listing `access_time`, `employee_id`, and `file_path`, calculating a cumulative `running_count` of all file accesses over time.

```SQL
-- My incorrect answer
SELECT 
    access_time,
    employee_id,
    file_path,
    COUNT(*) OVER (ORDER BY access_time) AS running_count
FROM access_logs
ORDER BY access_time
```

```SQL
-- The correct answer
SELECT access_time, 
       employee_id, 
       file_path, 
       COUNT(*) 
       OVER (ORDER BY access_time ROWS UNBOUNDED PRECEDING) AS running_count FROM access_logs 
ORDER BY access_time;
```

**87) The Percentile Analysis**

Isolate the heavy hitters. Segment the workforce by listing `employee_id` and `total_accesses`. Divide employees into 4 `quartile` groups based on their activity volume. Sort by total accesses descending.

```SQL
SELECT employee_id,
       COUNT(access_time) AS total_accesses,
       NTILE(4) OVER (
             ORDER BY COUNT(access_time)) AS quartile
FROM access_logs
GROUP BY employee_id
ORDER BY total_accesses DESC
```

**88) The Moving Average**

Spot the spikes above the baseline. Smooth the noise by listing `access_date` and `daily_count`. Calculate the `moving_avg_3day` (current day + 2 previous days) for file access counts.

```SQL
SELECT DATE(access_time) AS access_date,
       COUNT(*) AS daily_count,
       -- MA calc
       AVG(COUNT(*)) OVER (
           ORDER BY CAST(access_time AS DATE)
           ROWS BETWEEN 2 PRECEDING AND CURRENT ROW -- 2 preceding = 2 previous days and current row = current day
       ) AS moving_avg_3day
FROM access_logs
GROUP BY 1 -- Reprsent first column, which is access_date
ORDER BY 1
```

**89) The First and Last**

Get the bookends of the operation. Define the window for each `employee_id` by identifying their `first_file` and `first_time`, as well as their `last_file` and `last_time`.

```SQL
-- My incorrect answer
SELECT employee_id,
  FIRST_VALUE (file_path) OVER (  
    PARTITION BY employee_id 
    ORDER BY access_time ASC
  ) AS first_file,
  FIRST_VALUE (access_time) OVER (  
    PARTITION BY employee_id 
    ORDER BY access_time ASC
  ) AS first_time,
  LAST_VALUE (file_path) OVER (  
    PARTITION BY employee_id 
    ORDER BY access_time DESC
  ) AS last_file,
  LAST_VALUE (access_time) OVER (  
    PARTITION BY employee_id 
    ORDER BY access_time DESC
  ) AS last_time
FROM access_logs
GROUP BY employee_id
```

```SQL
-- The correct answer
SELECT DISTINCT employee_id, 
       FIRST_VALUE(file_path) OVER (
         PARTITION BY employee_id 
         ORDER BY access_time 
         ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
       ) AS first_file, 
       FIRST_VALUE(access_time) OVER (
         PARTITION BY employee_id 
         ORDER BY access_time 
         ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
       ) AS first_time, 
       LAST_VALUE(file_path) OVER (
         PARTITION BY employee_id 
         ORDER BY access_time 
         ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
       ) AS last_file, 
       LAST_VALUE(access_time) OVER (
         PARTITION BY employee_id 
         ORDER BY access_time 
         ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
       ) AS last_time 
FROM access_logs 
ORDER BY employee_id;
```

**90) The Insider Profile**

We need a name. Profile the insider by generating a dossier including `name`, `department`, `clearance_level`, `total_accesses`, `access_rank`, `first_access`, `last_access`, `hours_span`, and `avg_gap_minutes`. Assign a `threat_level`: 'HIGH RISK' (>5 accesses), 'MEDIUM RISK' (span > 12 hrs), else 'LOW RISK'. Sort by total accesses descending.

```SQL
-- My incorrect answer
WITH EmployeeBase AS (
     SELECT
         e.name,
         e.department,
         e.clearance_level,
         a.employee_id,
         a.access_time,
         COUNT(*) OVER(PARTITION BY a.employee_id) AS total_accesses,
         MIN(a.access_time) OVER(PARTITION BY a.employee_id) AS first_access,
         MAX(a.access_time) OVER(PARTITION BY a.employee_id) AS last_access
      FROM employees AS e
      JOIN access_logs a ON e.employee_id = a.employee_id
),

DossierData AS (
       SELECT DISTINCT
            name,
            department,
            clearance_level,
            total_accesses,
            first_access,
            last_access,
            (JULIANDAY(last_access) - JULIANDAY(first_access)) * 24 AS hours_span,
            CASE
               WHEN total_accesses > 1
               THEN ((JULIANDAY(last_access) - JULIANDAY(first_access)) * 1440) / (total_accesses - 1)
               ELSE 0
            END AS avg_gap_minutes
        FROM EmployeeBase
)
SELECT
     *,
     DENSE_RANK() OVER (ORDER BY total_accesses DESC) AS access_rank,
     CASE
        WHEN total_accesses > 5 THEN 'HIGH RISK'
        WHEN hours_span > 12 THEN 'MEDIUM RISK'
        ELSE 'LOW RISK'
     END AS threat_level
FROM DossierData
ORDER BY total_accesses DESC
```

```SQL
-- The correct answer (with explanation from Gemini3)

SELECT
    e.name,
    e.department,
    e.clearance_level,
    access_stats.total_accesses,
    access_stats.access_rank,
    access_stats.first_access,
    access_stats.last_access,
    access_stats.hours_span,
    access_stats.avg_gap_minutes,
    -- STEP 3: Assign threat levels based on behavioral thresholds
    CASE 
        WHEN access_stats.total_accesses > 5 THEN 'HIGH RISK'   -- Excessive scan volume
        WHEN access_stats.hours_span > 12 THEN 'MEDIUM RISK'   -- Abnormally long active period
        ELSE 'LOW RISK'
    END AS threat_level
FROM employees AS e
JOIN (
    /* STEP 2: Aggregate the raw log data into per-employee metrics */
    SELECT
        employee_id,
        COUNT(*) AS total_accesses,
        -- Rank employees by activity volume (1 = most active)
        RANK() OVER (ORDER BY COUNT(*) DESC) AS access_rank,
        MIN(access_time) AS first_access,
        MAX(access_time) AS last_access,
        -- Calculate total time between first and last scan (Julian Days * 24 = Hours)
        ROUND((JULIANDAY(MAX(access_time)) - JULIANDAY(MIN(access_time))) * 24, 2) AS hours_span,
        -- Average the individual gaps calculated in the inner subquery
        ROUND(AVG(gap_minutes), 2) AS avg_gap_minutes
    FROM (
        /* STEP 1: Calculate the time difference between every scan and the previous one */
        SELECT
            employee_id,
            access_time,
            -- LAG() looks at the previous row's timestamp for the same employee
            -- Result is converted from Julian Days to Minutes (* 24 * 60)
            (JULIANDAY(access_time) - JULIANDAY(LAG(access_time) OVER (
                PARTITION BY employee_id 
                ORDER BY access_time
            ))) * 24 * 60 AS gap_minutes
        FROM access_logs
    ) AS gaps
    GROUP BY employee_id
) AS access_stats ON e.employee_id = access_stats.employee_id
ORDER BY 
    access_stats.total_accesses DESC, 
    access_stats.hours_span DESC;
```

# CASE FILE: S10 - The Mastermind

![CASE FILE S10 - The Mastermind@2x.png](/img/sql-case-files-img/CASE%20FILE%20S10%20-%20The%20Mastermind@2x.png)

## Solution

**91) The Evidence Vault**

Nine seasons lead to this. Organize the board. Create a CTE `priority_suspects` for anyone with a `known_alias`. List their full dossier.

```SQL
WITH priority_suspects AS (
     SELECT *
     FROM suspects 
     WHERE known_alias IS NOT NULL
)
SELECT *
FROM priority_suspects
```

**92) The Connection Matrix**

We need the full picture. Cross-reference the files by creating two CTEs: one for suspects with aliases ('Has Alias') and one without ('No Alias'). Union them to list `suspect_id`, `name`, `known_alias`, and `status`. Sort by status, then name.

```SQL
WITH has_alias AS (
    SELECT 
          suspect_id,
          name,
          known_alias,
          'Has Alias' AS status
    FROM suspects
    WHERE known_alias IS NOT NULL
), 
no_alias AS (
SELECT 
          suspect_id,
          name,
          known_alias,
          'No Alias' AS status
    FROM suspects
    WHERE known_alias IS NULL
)

SELECT * FROM has_alias
UNION ALL
SELECT * FROM no_alias
ORDER BY status, name
```

**93) The Hierarchy Analysis**

Trace the chain of command from the penthouse to the street. Map the hierarchy using a recursive CTE to trace links from mentors to subordinates. List `suspect_id`, `name`, `known_alias`, `role`, and their `level` in the organization. Sort by level.

```SQL
WITH org_chart AS (
  SELECT
   suspect_id,
   name,
   known_alias,
   role,
   1 AS level
FROM suspects
WHERE mentor_id IS NULL

UNION ALL

SELECT
   s.suspect_id,
   s.name,
   s.known_alias,
   s.role,
   oc.level + 1 AS level
FROM suspects s
INNER JOIN org_chart oc
ON s.mentor_id = oc.suspect_id
)

SELECT
   suspect_id,
   name,
   known_alias,
   role,
   level
FROM org_chart
ORDER BY level
```

**94) The Evidence Aggregation**

How dangerous are they? Run the stats by creating a CTE to calculate aggregate suspect metrics. Report `total_suspects`, `aliased_suspects`, `clean_suspects`, `alias_percentage`, and a `threat_assessment`: 'High Risk Organization' (>50% aliased), 'Medium Risk Organization' (>25%), else 'Low Risk Organization'.

```SQL
WITH metrics AS (
    SELECT
    COUNT(suspect_id) AS total_suspects,
    COUNT(known_alias) AS aliased_suspects,
    SUM(
       CASE
         WHEN known_alias IS NULL
         THEN 1
         ELSE 0
         END) AS clean_suspects
    FROM suspects
)

SELECT
     total_suspects,
     aliased_suspects,
     clean_suspects,
     (aliased_suspects / total_suspects * 100) AS alias_percentage,
     CASE
        WHEN (aliased_suspects / total_suspects * 100) > 50     THEN 'High Risk Organization'
        WHEN (aliased_suspects / total_suspects * 100) > 25     THEN 'Medium Risk Organization'
        ELSE 'Low Risk Organization'
     END AS threat_assessment
FROM metrics
```

**95) The Cross-Case Analysis**

We take down the kings first. Prioritize targets by creating a CTE to calculate `total_severity` from evidence logs and rank suspects. List `name`, `known_alias`, `total_severity`, and `severity_rank` for the top 3. Sort by severity rank.

```SQL
WITH suspect_priority AS (
  SELECT
    s.name,
    s.known_alias,
    SUM(e.severity_score) AS total_severity,
    RANK() OVER (ORDER BY SUM(e.severity_score) DESC) AS severity_rank
FROM suspects AS s
JOIN evidence_log e
ON s.suspect_id = e.suspect_id
GROUP BY s.suspect_id, s.name, s.known_alias
)

SELECT
  name,
  known_alias,
  total_severity,
  severity_rank
FROM suspect_priority
WHERE severity_rank <= 3
ORDER BY severity_rank
```

**96) The Timeline Reconstruction**

Pinpoint the escalation. Reconstruct the timeline by creating a CTE to group evidence by `month`. List `month`, `evidence_count`, `total_severity`, and the `previous_month_count` to show the trend. Sort chronologically.

```SQL
-- My incorrect answer
WITH timeline AS (
    SELECT 
       CAST(strftime('%m', e.date_collected) AS INTEGER) AS month,
       COUNT(e.evidence_id) AS evidence_count,
       SUM(e.severity_score) AS total_severity
    FROM evidence_log AS e
    GROUP BY month
)
SELECT
   month,
   evidence_count,
   total_severity,
   LAG(evidence_count) OVER (ORDER BY month) AS previous_month_count
FROM timeline
ORDER BY month
```

```SQL
-- The correct answer 
WITH monthly_evidence AS (
    SELECT strftime('%Y-%m', date_collected) AS month,
      COUNT(*) AS evidence_count,
      SUM(severity_score) AS total_severity
    FROM evidence_log 
    GROUP BY strftime('%Y-%m', date_collected)
) 
SELECT 
    month,
    evidence_count,
    total_severity,
    LAG(evidence_count) OVER (ORDER BY month) AS previous_month_count 
FROM monthly_evidence 
ORDER BY month;
```

**97) The Network Mapping**

Who is working with whom? Map the clusters using CTEs to model the network. List `relationship_type`, `link_count`, and the specific `pairs` of suspects involved, ranked by frequency.

```SQL
-- My incorrect answer
WITH network AS (
  SELECT
  relationship_type,
  COUNT(associate_id) AS link_count,
CASE
  WHEN suspect_a_id = suspect_b_id
  THEN 1 ELSE 0
  END AS pairs
  FROM associates
  GROUP BY relationship_type
)

SELECT
  relationship_type,
  link_count,
  pairs
FROM network
ORDER BY link_count DESC
```

```SQL
-- The correct answer
WITH network_links AS (
    SELECT 
      s1.name AS suspect_a, 
      s2.name AS suspect_b, 
      a.relationship_type, 
      a.strength 
    FROM associates a 
    JOIN suspects s1 
    ON a.suspect_a_id = s1.suspect_id 
    JOIN suspects s2 
    ON a.suspect_b_id = s2.suspect_id
) 
SELECT 
    relationship_type, 
    COUNT(*) AS link_count, 
    GROUP_CONCAT(suspect_a || '-' || suspect_b, ', ') AS pairs 
FROM network_links 
GROUP BY relationship_type 
ORDER BY link_count DESC;
```

**98) The Risk Assessment Matrix**

No surprises. Assess the total threat using multiple CTEs to combine evidence and network scores into a `total_risk`. List suspect details and assign a `risk_level`: 'CRITICAL' (>30), 'HIGH' (>15), else 'MEDIUM'. Sort by total risk descending.

```SQL
-- My incorrect answer
WITH evidence_score AS (
  SELECT 
     suspect_id,
     SUM(severity_score) AS total_severity
     FROM evidence_log
     GROUP BY suspect_id
),
network_score AS (
  SELECT
     suspect_id,
     COUNT(*) AS link_count
  FROM (
    SELECT 
       suspect_a_id AS suspect_id FROM associates
       UNION ALL
       suspect_b_id AS suspect_id FROM associates
) AS all_links
GROUP BY suspect_id
),
risk_calc AS (
    SELECT
        s.name,
        s.known_alias,
        COALESCE(e.total_severity, 0) + COALESCE(n.link_count, 0) AS total_risk
  FROM suspects s
  LEFT JOIN evidence_score e
  ON s.suspect_id = e.suspect_id
  LEFT JOIN network_score n
  ON s.suspect_id = n.suspect_id
)
SELECT 
   CASE 
     WHEN total_risk > 30 THEN 'CRITICAL'
     WHEN total_risk > 15 THEN 'HIGH'
   ELSE 'MEDIUM'
   END AS risk_level
FROM risk_calc
ORDER BY total_risk DESC
```

```SQL
-- The correct answer
WITH evidence_scores AS (
    SELECT 
      suspect_id, 
      SUM(severity_score) AS evidence_score 
    FROM evidence_log 
    GROUP BY suspect_id
), 
network_scores AS (
    SELECT 
      suspect_a_id AS suspect_id, 
      COUNT(*) * 5 AS network_score 
    FROM associates 
    GROUP BY suspect_a_id
), 
risk_calculation AS (
    SELECT 
      s.name, 
      s.known_alias, 
      COALESCE(e.evidence_score, 0) + COALESCE(n.network_score, 0) AS total_risk 
    FROM suspects s 
    LEFT JOIN evidence_scores e 
    ON s.suspect_id = e.suspect_id 
    LEFT JOIN network_scores n 
    ON s.suspect_id = n.suspect_id
) 
SELECT *, 
    CASE 
      WHEN total_risk > 30 THEN 'CRITICAL' 
      WHEN total_risk > 15 THEN 'HIGH' 
      ELSE 'MEDIUM' 
    END AS risk_level 
FROM risk_calculation 
ORDER BY total_risk DESC;
```

**99) The Final Dossier**

Make it airtight. Compile the dossier using CTEs to aggregate counts for 'Suspects', 'Evidence Items', and 'Network Links'. Add a specific entry for the 'Primary Target'. List `category` and `value`.

```SQL
-- My incorrect answer

WITH suspect_count AS (
     SELECT 'Suspects' AS category, CAST(COUNT(*) AS CHAR) AS value
     FROM suspects
),
evidence_count AS (
     SELECT 'Evidence' AS category, CAST(COUNT(*) AS CHAR) AS value
     FROM evidence_log
),
link_count AS (
     SELECT 'Network Links' AS category, CAST(COUNT(*) AS CHAR) AS value
     FROM associates
),
primary_target AS (
     SELECT 
        'Primary Target' AS category,
         name AS value
         FROM suspects s
         JOIN (
              SELECT 
                 suspect_id,
                 COUNT(*) AS evidence_hits
              FROM evidence_log
              GROUP BY suspect_id
              ORDER BY evidence_hits DESC
              LIMIT 1
           ) top_sus ON s.suspect_id = top_sus.suspect_id
)

SELECT * FROM suspect_count
UNION ALL
SELECT * FROM evidence_count
UNION ALL
SELECT * FROM link_count
UNION ALL
SELECT * FROM primary_target
```

```SQL
-- The correct answer
WITH summary_stats AS (
    -- Collect the bulk counts for the organization
    SELECT 
        'Suspects' AS category, 
        COUNT(*) AS value 
    FROM suspects

    UNION ALL 
    
    SELECT 
        'Evidence Items', 
        COUNT(*) 
    FROM evidence_log
    
    UNION ALL 
    
    SELECT 
        'Network Links', 
        COUNT(*) 
    FROM associates
), 

top_target AS (
    -- Format the specific high-value target string
    SELECT 
        name || ' (' || known_alias || ')' AS target 
    FROM suspects 
    WHERE suspect_id = 1
)

-- Assemble the final dossier
SELECT * FROM summary_stats
UNION ALL 
SELECT 'Primary Target', target FROM top_target;
```

**100) The Architect's Fall**

This is it. Execute the warrant. Combine recursive hierarchy and evidence aggregation. List `name`, `role`, `level`, `evidence_items`, `severity`, and `status` ('PRIMARY TARGET' or 'ACCOMPLICE'). Sort by level, then by severity descending.

```SQL
-- My incorrect answer
WITH RECURSIVE summary_stats AS (
    SELECT 
        suspect_id, 
        name, 
        role, 
        1 AS level
    FROM suspects
    WHERE mentor_id IS NULL

    UNION ALL
    
    SELECT 
        s.suspect_id, 
        s.name, 
        s.role, 
        ss.level + 1
    FROM suspects s
    INNER JOIN summary_stats ss ON s.mentor_id = ss.suspect_id
),
evidence_agg AS (
    SELECT 
        suspect_id, 
        COUNT(*) AS evidence_items, 
        SUM(severity_score) AS total_severity
    FROM evidence_log
    GROUP BY suspect_id
)

SELECT 
    ss.name, 
    ss.role, 
    ss.level, 
    COALESCE(e.evidence_items, 0) AS evidence_items, 
    COALESCE(e.total_severity, 0) AS severity,
    CASE 
        WHEN ss.level = 1 THEN 'PRIMARY TARGET' 
        ELSE 'ACCOMPLICE' 
    END AS status
FROM summary_stats ss
LEFT JOIN evidence_agg e ON ss.suspect_id = e.suspect_id
ORDER BY ss.level ASC, severity DESC;
```

```SQL
-- The correct answer
WITH RECURSIVE command_chain AS (
    -- Anchor: Start with the top-tier Leader
    SELECT 
        suspect_id, 
        name, 
        role, 
        0 AS level 
    FROM suspects 
    WHERE role = 'Leader'
    
    UNION ALL
    
    -- Recursive: Link subordinates to their mentors
    SELECT 
        s.suspect_id, 
        s.name, 
        s.role, 
        c.level + 1 
    FROM suspects s 
    JOIN command_chain c ON s.mentor_id = c.suspect_id
), 

evidence_summary AS (
    -- Aggregate crime stats per suspect
    SELECT 
        suspect_id, 
        COUNT(*) AS evidence_count, 
        SUM(severity_score) AS total_severity 
    FROM evidence_log 
    GROUP BY suspect_id
)

-- Final dossier assembly
SELECT 
    c.name, 
    c.role, 
    c.level, 
    COALESCE(e.evidence_count, 0) AS evidence_items, 
    COALESCE(e.total_severity, 0) AS severity, 
    CASE 
        WHEN c.level = 0 THEN 'PRIMARY TARGET' 
        ELSE 'ACCOMPLICE' 
    END AS status 
FROM command_chain c 
LEFT JOIN evidence_summary e ON c.suspect_id = e.suspect_id 
ORDER BY 
    c.level ASC, 
    severity DESC;
```

# Thoughts

- This SQL game is great for practicing as a beginner. Especially in the sector of fraud detection. The question is too short, and the later level requires advanced SQL functions and knowledge. Games like these are a very good start for beginners to keep you entertained and learning at the same time.