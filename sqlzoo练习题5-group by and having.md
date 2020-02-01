---
title: sqlzoo练习题5-group by and having
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-09 19:28:12
password:
summary:
copyright: true
tags: 
  - sqlzoo
  - group by
  - having
  - 数据库
categories:
  - MySQL
  - sqlzoo练习
---

>GROUP BY` and `HAVING
>
>By including a `GROUP BY` clause functions such as `SUM` and `COUNT` are applied to groups of items sharing values. When you specify `GROUP BY continent` the result is that you get only one row for each different value of `continent`. All the other columns must be "aggregated" by one of `SUM`, `COUNT` ...
>
>The `HAVING` clause allows use to filter the groups which are displayed. The `WHERE` clause filters rows before the aggregation, the `HAVING` clause filters after the aggregation.

- where 过滤在前
- group by 中间
- having 过滤在后

<!--MORE-->

```sql
-- 1
SELECT continent, COUNT(name) 
FROM world
GROUP BY continent

-- 2
SELECT continent, SUM(population)
FROM world
GROUP BY continent

-- 3
/*WHERE and GROUP BY. The WHERE filter takes place before the aggregating function. For each relevant continent show the number of countries that has a population of at least 200000000.*/
select continent, count(name)
from world 
where population > 200000000
group by continent;

-- 4
/*GROUP BY and HAVING. The HAVING clause is tested after the GROUP BY. You can test the aggregated values with a HAVING clause. Show the total population of those continents with a total population of at least half a billion.*/
SELECT continent, SUM(population)
FROM world
GROUP BY continent               
HAVING SUM(population) > 500000000   -- 分组之后再把大于50000000的筛选出来

-- 5
/*For each continent show the continent and number of countries.*/
select continent, count(name)   -- 分清sum 和 count的关系
from world
group by continent;

-- 6
/*For each continent show the continent and number of countries with populations of at least 10 million.*/
SELECT continent, COUNT(name)
FROM world
WHERE population >= 10000000
GROUP BY continent

-- 7
/*List the continents that have a total population of at least 100 million.*/
select continent
from world 
group by continent
having sum(population) >= 100000000;
```

