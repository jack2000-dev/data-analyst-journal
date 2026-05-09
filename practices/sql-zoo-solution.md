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

# SELECT in SELECT

## 1. Bigger than Russia

```SQL
-- Solved
SELECT name
FROM world
WHERE population >
     (SELECT population FROM world
      WHERE name='Russia')
```

## 2.  Richer than UK

```SQL
-- Error
SELECT name
FROM world
WHERE continent = 'Europe' 
AND gdp / population >
      (SELECT gdp / population
       FROM world
       WHERE continent = 'United Kingdom')
```

```SQL
-- Solved
SELECT name
FROM world
WHERE continent = 'Europe' 
AND gdp / population >
      (SELECT gdp / population
       FROM world
       WHERE name = 'United Kingdom')
```

## 3. Neighbours of Argentina and Australia

```SQL
-- Error
SELECT name, continent
FROM world
WHERE name IN

(SELECT continent
FROM world
WHERE name IN ('Argentina', 'Australia')
)
ORDER BY name
```

```SQL
-- Solved
SELECT name, continent
FROM world
WHERE continent IN
    (SELECT continent
     FROM world
     WHERE name IN ('Argentina', 'Australia')
    )
ORDER BY name
```

## 4. Between Canada and Poland

```SQL
-- Solved
SELECT name, population
FROM world
WHERE population > 
(SELECT population FROM world WHERE name = 'United Kingdom')
AND population < (SELECT population FROM world WHERE name = 'Germany');
```

## 5. Percentages of Germany

```SQL
-- Solved
SELECT
  name,
  CONCAT(ROUND(
    population / 
    (SELECT population 
     FROM world 
     WHERE name = 'Germany'
    ) * 100, 0), '%') AS percentage
FROM world
WHERE continent = 'Europe'
```

## 6. Bigger than every country in Europe

```SQL
SELECT
  name
FROM world
WHERE gdp > ALL(SELECT gdp
                  FROM world
                  WHERE continent = 'Europe')
```

## 7. Largest in each continent

```SQL
-- Solved
SELECT continent, name, area 
FROM world w1
WHERE area >= ALL
    (SELECT area 
     FROM world w2
     WHERE w2.continent=w1.continent
    )
```

## 8. First country of each continent (alphabetically)

```SQL
-- Solved
SELECT continent, name
FROM world w1
WHERE name <= ALL
    (SELECT name 
     FROM world w2
     WHERE w1.continent = w2.continent)
```

## 9. Difficult Questions That Utilize Techniques Not Covered In Prior Sections

```SQL
-- Solved
SELECT name, continent, population
FROM world
WHERE population <= 25000000 
AND name = ALL (SELECT name FROM world WHERE population <= 25000000)
```

## 10. Three times bigger

```SQL
-- Solved
SELECT
  name,
  continent
FROM world w1
WHERE population > ALL
    (SELECT population * 3 
     FROM world w2
     WHERE w1.continent = w2.continent
     AND w1.name <> w2.name)
```

# SUM and COUNT

## 1. Total World Population

```SQL
-- Solved
SELECT SUM(population)
FROM world
```

## 2. List of Continents

```SQL
-- Solved
SELECT DISTINCT continent
FROM world
```

## 3. GDP of Africa

```SQL
-- Solved
SELECT SUM(gdp)
FROM world
WHERE continent = 'Africa'
GROUP BY continent
```

## 4. Count the big countries

```SQL
-- Solved
SELECT COUNT(name) AS large_country_count
FROM world
WHERE area >= 1000000
```

## 5. Baltic states population

```SQL
-- Solved
SELECT SUM(population)
FROM world
WHERE name IN ('Estonia', 'Latvia', 'Lithuania')
```

## 6. Counting the countries of each continent

```SQL
-- Solved
SELECT continent, COUNT(name) AS number_of_contries
FROM world
GROUP BY continent
```

## 7. Counting big countries in each continent

```SQL
-- Solved
SELECT continent, COUNT(name) AS number_of_countries
FROM world
WHERE population >= 10000000
GROUP BY continent
```

## 8. Counting big continents

```SQL
-- Solved
SELECT continent
FROM world
GROUP BY continent
HAVING SUM(population) > 100000000
```

# JOIN

## 1. Bender Goals

```SQL
-- Solved
SELECT matchid, player 
FROM goal 
WHERE teamid LIKE 'GER%'
```

## 2. Team names

```SQL
-- Solved
SELECT g.id,g.stadium,g.team1,g.team2
FROM game g
WHERE g.id = '1012'
```

## 3. JOIN

```SQL
-- Solved
SELECT player,teamid, stadium, mdate
FROM game 
JOIN goal ON (id=matchid)
WHERE teamid = 'GER'
```

## 4. Mario goals

```SQL
-- Solved
SELECT team1, team2, player
FROM game 
JOIN goal ON (id=matchid)
WHERE player LIKE 'Mario%'
```

## 5 . Early goals

```SQL
-- Solved
SELECT g.player, g.teamid, t.coach, g.gtime
  FROM goal g
JOIN eteam t
ON g.teamid = t.id
 WHERE g.gtime<=10
```

## 6. Fernando Santos

```SQL
-- Solved
SELECT g.mdate, t.teamname
FROM game g
JOIN eteam t
ON g.team1 = t.id
WHERE t.coach = 'Fernando Santos'
```

## 7. Games in Warsaw

```SQL
-- Solved
SELECT player
FROM game g
JOIN goal gl
ON g.id = gl.matchid
WHERE stadium = 'National Stadium, Warsaw'
```

## 8 . Who scored against Germany

```SQL
-- Error
SELECT DISTINCT gl.player
FROM game g
JOIN goal gl
ON g.id = gl.matchid
WHERE g.team1 != 'GER' AND g.team2 != 'GER'
```

```SQL
-- Solved
SELECT DISTINCT player
FROM game 
JOIN goal ON matchid = id
WHERE (team1 = 'GER' OR team2 = 'GER') 
  AND teamid <> 'GER';
```

## 9. Total goals scored

```SQL
-- Solved
SELECT teamname, COUNT(*)
FROM eteam
JOIN goal ON id = teamid
GROUP BY teamname
```

## 10. Goals by stadium

```SQL
-- Solved
SELECT g.stadium, COUNT(gl.gtime)
FROM game g
JOIN goal gl
ON g.id = gl.matchid
GROUP BY g.stadium
```

## 11. Poland's games

```SQL
-- Solved
SELECT gl.matchid, g.mdate, COUNT(gl.gtime)
FROM game g
JOIN goal gl
ON gl.matchid = g.id 
WHERE (g.team1 = 'POL' OR g.team2 = 'POL')
GROUP BY gl.matchid, g.mdate
```

## 12. Germany scores

```SQL
-- Solved
SELECT matchid, mdate, COUNT(gtime)
FROM game
JOIN goal
ON matchid = id
WHERE teamid = 'GER' 
GROUP BY matchid, mdate
```

## 13. All scores

```SQL
-- Error
SELECT mdate,
       team1,
       SUM(CASE WHEN teamid = team1 THEN 1 ELSE 0 END) AS score1,
       team2,
       CASE WHEN teamid = team2 THEN 1 ELSE 0 END AS score2
FROM game 
JOIN goal ON matchid = id
GROUP BY mdate, matchid, team1, team2, teamid
ORDER BY mdate, matchid, team1, team2
```

```SQL
-- Solved
SELECT mdate,
       team1,
       SUM(CASE WHEN teamid = team1 THEN 1 ELSE 0 END) AS score1,
       team2,
       SUM(CASE WHEN teamid = team2 THEN 1 ELSE 0 END) AS score2
FROM game 
LEFT JOIN goal ON matchid = id
WHERE team1 = 'ENG' OR team2 = 'ENG'
GROUP BY mdate, matchid, team1, team2
ORDER BY mdate, matchid, team1, team2;
```

# More JOIN

## 1. 1962 movies

```SQL
-- Error (possibly bug in the db)
SELECT id, title
 FROM movie
 WHERE yr=1962 AND budget > 2000000
```

## 2. When was Citizen Kane released?

```SQL
-- Solved
SELECT yr
FROM movie
WHERE title = 'Citizen Kane'
```

## 3. Star Trek movies

```SQL
-- Solved
SELECT id, title, yr
FROM movie
WHERE title LIKE 'Star Trek%'
ORDER BY yr
```

## 4. id for actor Glenn Close

```SQL
-- Solved
SELECT id
FROM actor
WHERE name = 'Glenn Close'
```

## 5. id for Casablanca

```SQL
-- Solved
SELECT id
FROM movie
WHERE title = 'Casablanca' AND yr = 1942
```

## 6.  Cast list for Casablanca

```SQL
-- Solved
SELECT name
FROM actor a 
JOIN casting c
ON id = actorid
WHERE movieid = 132689
```

## 7. Alien cast list

```SQL
-- Solved
SELECT name
FROM actor a
JOIN casting c
ON id = actorid
WHERE movieid = 103569
```

## 8. Harrison Ford movies

```SQL
-- Solved
SELECT title
 FROM movie
 JOIN casting ON movie.id = casting.movieid
 JOIN actor   ON casting.actorid = actor.id
 WHERE actor.name = 'Harrison Ford';
```

## 9 . Harrison Ford as a supporting actor

```SQL
-- Solved
SELECT title
 FROM movie
 JOIN casting ON movie.id = casting.movieid
 JOIN actor   ON casting.actorid = actor.id
 WHERE actor.name = 'Harrison Ford' AND casting.ord != 1
```

## 10. Lead actors in 1962 movies

```SQL
-- Error
SELECT title, name
 FROM movie
 JOIN casting ON movie.id = casting.movieid
 JOIN actor   ON casting.actorid = actor.id
 WHERE yr = 1962 
   AND ord = 1
```

## 11. Busy year for Rock Hudson

```SQL
-- Solved
SELECT yr,COUNT(title) FROM
  movie JOIN casting ON movie.id=movieid
        JOIN actor   ON actorid=actor.id
WHERE name='Rock Hudson'
GROUP BY yr
HAVING COUNT(title) > 2
```

## 12. Lead actor in Julie Andrews movies

```SQL
-- Solved
SELECT title, name
 FROM movie
 JOIN casting ON movie.id = casting.movieid
 JOIN actor   ON casting.actorid = actor.id
 WHERE ord = 1 
   AND movieid IN (
     SELECT movieid 
     FROM casting
     JOIN actor ON casting.actorid = actor.id
     WHERE name = 'Julie Andrews'
   )
```

## 13. Actors with 15 leading roles

```SQL
-- Solved
SELECT name
FROM actor a
JOIN casting c ON a.id = c.actorid
WHERE c.ord = 1
GROUP BY name
HAVING COUNT(*) >= 15
ORDER BY name ASC
```

## 14. released in the year 1978

```SQL
-- Solved
SELECT title, COUNT(actorid)
 FROM movie
 JOIN casting ON movie.id = casting.movieid
 WHERE yr = 1978
 GROUP BY title
 ORDER BY COUNT(actorid) DESC, title
```

## 15.  with 'Art Garfunkel'

```SQL
-- Solved
SELECT DISTINCT name
 FROM actor
 JOIN casting ON actor.id = casting.actorid
 WHERE movieid IN (
   SELECT movieid FROM casting
   JOIN actor ON actor.id = casting.actorid
   WHERE name = 'Art Garfunkel')
  AND name != 'Art Garfunkel'
 ORDER BY name
```