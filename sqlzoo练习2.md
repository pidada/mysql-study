---
title: sqlzoo练习2
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-13 16:19:52
password:
summary:
tags:
  - sql
  - sqlzoo
categories:
  - MySQL
  - sqlzoo练习
---

主要练习的是`select`语句

- round函数
- left函数
- 通配符的使用

<!--MORE-->

### 表结构

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gaurovfhlaj30pa0cgdhe.jpg)

### 练习

1. show the name, continent and population of all countries.

```sql
SELECT name, continent, population FROM world
```

2. Show the name for the countries that have a population of at least 200 million. 200 million is 200000000, there are eight zeros.

- Million：6个0
- Billion：8个0

```sql
SELECT name FROM world
WHERE population > 200000000;
```



3. 关于人均GDP

Give the `name` and the **per capita GDP** for those countries with a `population` of at least 200 million.

```sql
select name, GDP/population  -- 计算人均GDP
from world
where population >= 200000000;
```

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gauzzvej04j312s0f2ad9.jpg)



4. Show the `name` and `population` in millions for the countries of the `continent` 'South America'. Divide the population by 1000000 to get population in millions.

```sql
select name, population/1000000
from world
where continent = 'South America';
```

5. Show the `name` and `population` for France, Germany, Italy

```sql
select name, population
from world
where name in ('France', 'Germany', 'Italy');
```

6. Show the countries which have a `name` that includes the word 'United'

```sql
select name
from world
where name like '%United%';
```



7. Two ways to be big: A country is **big** if it has an area of more than 3 million sq km or it has a population of more than 250 million.

   Show the countries that are big by area or big by population. Show name, population and area.

```sql
select name, population, area
from world
where area > 3000000 or population > 250000000;
```

8. Exclusive OR (XOR). Show the countries that are big by area (more than 3 million) or big by population (more than 250 million) **but not both.** Show name, population and area.

> 两个条件不能同时满足

```sql
select name, population, area
from world
where area > 3000000 xor population > 250000000;
```

9. Show the `name` and `population` in millions and the GDP in billions for the countries of the `continent` 'South America'. Use the [ROUND](https://sqlzoo.net/wiki/ROUND) function to show the values to two decimal places.

   For South America show population in millions and GDP in billions both to 2 decimal places.

```sql
select name, round(population / 1000000, 2), round(gdp / 1000000000, 2)
from world
where continent = 'South America';
```



#### 关于round函数的使用

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gauyro9ifyj30y00mstbu.jpg)

- 后面的第二个参数如果是正数，表示的保留几位有效小数
- 如果是负数，表示的是取整数，保留几个0

10. Show the `name` and per-capita GDP for those countries with a GDP of at least one trillion (1000000000000; that is 12 zeros). Round this value to the nearest 1000.

    Show per-capita GDP for the trillion dollar countries to the nearest $1000.

> 笔记：关于人均GDP的问题

```sql
select name, round(gdp/population, -3)
from world
where gdp >= 1000000000000;
```



11. Show the name and capital where the name and the capital have the same number of characters.
    - You can use the [LENGTH](https://sqlzoo.net/wiki/LENGTH) function to find the number of characters in a string

```sql
SELECT name,  capital
FROM world
WHERE length(name) = length(capital);
```



12. Show the name and the capital where the first letters of each match. Don't include countries where the name and the capital are the same word.
    - You can use the function [LEFT](https://sqlzoo.net/wiki/LEFT) to isolate the first character.
    - You can use `<>` as the **NOT EQUALS** operator.

```sql
SELECT name, capital
FROM world
where LEFT(name,1) = LEFT(capital, 1)
and name <> capital;
```

- 判断左边的几个字母相同`left`函数
- 使用不等于符号`<>`

13. Find the country that has all the vowels and no spaces in its name.
    - You can use the phrase `name NOT LIKE '%a%'` to exclude characters from your results.
    - The query shown misses countries like Bahamas and Belarus because they contain at least one 'a'

```sql
SELECT name
FROM world
WHERE name LIKE '%a%'
     AND name LIKE '%e%'
     AND name LIKE '%i%'
     AND name LIKE '%o%'
     AND name LIKE '%u%'
     AND name NOT LIKE '% %';  -- 不能带有空格
```

- 国家名字中带有**全部的元音字母**
- 名字中间**不能有空格**
