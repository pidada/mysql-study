---
title: sqlzoo练习1
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-13 11:43:09
password:
summary:
tags:
  - sql
  - sqlzoo
categories:
  - MySQL
  - sqlzoo练习
---

开始进行`SQL`的练习，选择了[sqlzoo](https://sqlzoo.net/wiki/SELECT_basics)网站

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gausf1xd7lj312p0u0wlg.jpg)

<!--MORE-->

### 表结构

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gaurovfhlaj30pa0cgdhe.jpg)

### 练习题

主要是练习`where`语句的使用

```sql
-- 1
SELECT population FROM world
WHERE name = 'Germany'

-- 2
SELECT name, population FROM world
WHERE name IN ('Sweden', 'Norway','Denmark');

-- 3
SELECT name, area FROM world
WHERE area BETWEEN 200000 AND 250000
```



### quiz

1. 产生下表的结果

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gaus0y1igej30hi07e3z4.jpg)

```sql
select name, population
from world
wher populantion between 1000000 and 1250000
```

2. 通配符使用

```sql
select name, population
from world
where name like "AL%"  -- AL开头的名字
```

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gaus3cfxhdj30de05qglw.jpg)

3.  Select the code which shows the countries that end in A or L

```sql
select name
from world
where name like "a%" or name like "%l"
```

4. 代码运行结果

```sql
select name, length(name)
from world
where length(name)=5 and region="Europe"
```

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gaus6sgv2xj30cu078aab.jpg)

5. 代码运行结果

```sql
select name, area*2
from world
where population = 64000
```

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gaus8am5qkj30oy09q3zp.jpg)

6. Select the code that would show the countries with an area larger than 50000 and a population smaller than 10000000

```sql
select name, area, population
from world
where area > 50000 and population < 1000000;
```

7. Select the code that shows the population density of China, Australia, Nigeria and France

> 笔记：题目中要求的是人口密度

```sql
select name, population / area
from world
where name in ('China', 'Nigeria', 'France', 'Australia')
```


