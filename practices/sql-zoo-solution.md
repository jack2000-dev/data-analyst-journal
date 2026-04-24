---
Created: 2026-04-23
tags:
  - sql
  - practice
---

# Introduction
[SQLZoo](https://sqlzoo.net/) includes tutorials and references to support people learning SQL. The site is maintained by Andrew Cumming a software developer at Intelligent Growth Solutions based in Edinburgh, UK. (Thank you Andrew!)

See also: [No-ads version](https://noads.sqlzoo.net/wiki/SQL_Tutorial)

Below are my solution, notes, and approach I took. You can follow along this tutorial [here](https://sqlzoo.net/wiki/SELECT_from_WORLD_Tutorial). Note that this excluded quizzes.

# SELECT from world

## 1. Introduction

```SQL
SELECT name, continent, population FROM world
```

## 2. Large Countries

```SQL
SELECT name FROM world
WHERE population > 200000000
```

## 3. Per capita GDP

```SQL
SELECT 
  name,
  gdp / population
FROM world
WHERE population > 200000000
```

*Note: I relearned about part/whole and whole/part math concept*

## 4. South America In millions

```SQL
SELECT
  name,
  population / 1000000 AS population
FROM world
WHERE continent = 'South America'
```

## 5. France, Germany, Italy

```SQL
SELECT 
  name,
  population
FROM world
WHERE name IN ('France', 'Germany', 'Italy')
```

*Note: I didn't know how to use `IN` properly at first but I look it up [here](https://www.geeksforgeeks.org/sql/sql-where-clause/)*

## 6. United

```SQL
SELECT
  name
FROM world
WHERE name LIKE '%United%'
```

## 7. Two ways to be big

```SQL
SELECT
  name,
  population,
  area
FROM world
WHERE
  area > 3e6 OR population > 250e6
```

## 8. One or the other (but not both)

```SQL
SELECT
  name,
  population,
  area
FROM world
WHERE
  area > 3e6 XOR population > 250e6
```

*Note: This one makes me learn more about the bitwise, OR logic gate, and Exclusive OR [(XOR)](https://www.geeksforgeeks.org/sql/bit_xor-function-in-mysql/) concepts.*

## 9. Rounding

```SQL
SELECT
  name,
  ROUND(population / 1000000.0, 2) AS population,
  ROUND(gdp / 1000000000.0, 2) AS gdp
FROM world
WHERE
  continent = 'South America'
```

## 10. Trillion dollar economies

```SQL
SELECT
  name,
  ROUND(gdp / population, -3) AS gdp_cap
FROM world
WHERE
  gdp > 1e12
```

## 11. Name and capital have the same length

```SQL
SELECT name, capital
  FROM world
 WHERE LENGTH(name) = LENGTH(capital)
```

## 12. Matching name and capital

```SQL
SELECT name, capital
FROM world
WHERE LEFT(name, 1) = LEFT(capital, 1)
  AND name <> capital
```

*Note: Make sure to read the problems twice. This is the first time I learned about `LEFT()` function*

## 13. All the vowels

```SQL
SELECT name
  FROM world
 WHERE name LIKE '%a%'
   AND name LIKE '%e%'
   AND name LIKE '%i%'
   AND name LIKE '%o%'
   AND name LIKE '%u%'
   AND name NOT LIKE '% %';
```

# SELECT from nobel

## 1. Winners from 1950

```SQL
SELECT yr, subject, winner
  FROM nobel
 WHERE yr = 1950
```

## 2. 1962 Literature

```SQL
SELECT winner
  FROM nobel
 WHERE yr = 1962
   AND subject = 'literature'
```

## 3. Albert Einstein

```SQL
SELECT
  yr,
  subject
FROM nobel
WHERE winner = 'Albert Einstein'
```

## 4. Recent Peace Prizes

```SQL
SELECT
  winner
FROM nobel
WHERE subject = 'Peace' 
AND yr >= 2000
```

## 5. Literature in the 1980's

```SQL
SELECT *
FROM nobel
WHERE subject = 'literature' 
AND yr BETWEEN '1980' AND '1989'
```

## 6. Only Presidents

```SQL
SELECT * 
FROM nobel
WHERE winner 
IN ('Theodore Roosevelt', 'Thomas Woodrow Wilson', 'Jimmy Carter', 'Barack Obama')
```

## 7.  John

```SQL
SELECT winner
FROM nobel
WHERE winner LIKE 'John%'
```

## 8. Chemistry and Physics from different years

```SQL
SELECT
  yr,
  subject,
  winner
FROM nobel
WHERE (subject = 'physics' AND yr = '1980')
OR (subject = 'chemistry' AND yr = '1984')
```

## 9. Exclude Chemists and Medics

```SQL
SELECT
  yr,
  subject,
  winner
FROM nobel
WHERE subject NOT IN ('chemistry', 'medicine') AND yr = 1980
```

## 10. Early Medicine, Late Literature

```SQL
SELECT
  yr,
  subject,
  winner
FROM nobel
WHERE (subject = 'Medicine' AND yr < 1910)
OR (subject = 'Literature' AND yr >= 2004)
```

## 11. Umlaut

```SQL
SELECT *
FROM nobel
WHERE winner = 'PETER GRÜNBERG'
```

## 12. Apostrophe

```SQL
SELECT *
FROM nobel
WHERE winner = "EUGENE O'NEILL"
```

## 13. Knights of the realm

```SQL
SELECT
  winner,
  yr,
  subject
FROM nobel
WHERE winner LIKE 'Sir%'
ORDER BY yr DESC, winner ASC;
```

## 14. Chemistry and Physics last

```SQL
SELECT winner, subject
  FROM nobel
 WHERE yr=1984
 ORDER BY 
subject IN ('physics','chemistry'),
subject,
winner
```

