---
title: sqlzoo练习题2
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-04 19:10:17
password:
summary:
copyright: true
tags: 
  - sqlzoo
  - sql查询
  - 数据库
categories:
  - MySQL
  - sqlzoo练习
---

> `sqlzoo`的 第二次练习，主要需要掌握的知识点：
>
> - `left()`字符串左边
> - `round()`取整函数
> - `like`的使用需要熟悉
> - 通配符`%`的使用

<!--MORE-->

```sql
SELECT name, continent, population FROM world;

SELECT name FROM world WHERE population >= 200000000;

select name, gdp/population from world where population >= 200000000;  -- 如何计算人均GDP

select name, population from world  where name in ('France', 'Germany', 'Italy');

select name  from world  where name like '%United%';   -- 模糊查询

select name, population, area  from world  where area > 3000000 or population > 250000000;   -- or

select name, population, area from world where area > 3000000 xor population > 250000000;   -- xor

select name,  round(population / 1000000, 2), round(gdp/ 1000000000, 2) from world where continent = 'South America';  -- 掌握round函数的用法

select name, round(gdp / population, -3) from world where gdp >= 1000000000000;

SELECT name, capital FROM world WHERE length(name) = length(capital);

SELECT name, capital FROM world where (left(name,1) = left(capital,1)) and (name <> capital);  -- left()函数的用法，表示左边的字符left(name, 1) 表示最左边的字符

SELECT name  FROM world 
WHERE name LIKE '%a%' AND   -- 表示包含多个字母
name  LIKE '%e%' AND 
name  LIKE '%i%' AND 
name  LIKE '%o%' AND 
name  LIKE '%u%' AND 
name NOT LIKE '% %'  -- 表示不包含空格
```

