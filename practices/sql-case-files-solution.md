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