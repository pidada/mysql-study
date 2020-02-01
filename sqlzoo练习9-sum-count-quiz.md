---
title: sqlzoo练习9-sum-count-quiz
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-18 21:40:30
password:
summary:
tags:
  - sqlzoo
  - sum 
  - count
categories:
  - MySQL
  - sqlzoo练习
---

> 本文中主要是对sum and count进行了一个小测，熟悉`SQL`中聚合函数的使用

### Sum and Count Quiz

![image-20200115233840316](https://tva1.sinaimg.cn/large/006tNbRwgy1gaxo95g9i0j30sg0cmabg.jpg)

<!--MORE-->



### 练习

1. Select the statement that shows the sum of population of all countries in 'Europe'

> 欧洲所有国家的总人口

```sql
select sum(population)
from bbc
where region='Europe';
```

2. Select the statement that shows the number of countries with population smaller than 150000

> 统计人口小于150000的国家总数

```sql
select count(name)
from bbc
where population < 150000;
```

3. Select the list of core SQL aggregate functions

> 列出SQL中aggregate函数，返回的是单一结果的函数

```sql
AVG(), COUNT(), CONCAT(), FIRST(), LAST(), MAX(), MIN(), SUM()
```

4. Select the result that would be obtained from the following code，根据代码选择结果

```sql
select region, sum(area)
from bbc
where sum(area) > 15000000  -- 错误的写法
group by region;
```

- area总数大于15000000；（写法错误）
- 根据地区region进行分组

**原因：where无法对区域总和进行分组，需要使用having来过滤行**

正确写法：

```sql
select region, sum(area)
from bbc
group by region
having sum(area) > 15000000;  --使用having进行过滤分组
```

5. Select the statement that shows the average population of 'Poland', 'Germany' and 'Denmark'

> 求解3个国家的平均人口数

```sql
select avg(population)
from bbc
where name in ('Poland', 'Germany', 'Denmark')
```

6. Select the statement that shows the medium population density of each region

> 显示每个region的平均人口密度

```sql
select region, sum(population)/sum(area) as density
from bbc
group by region;
```

7. Select the statement that shows the name and population density of the country with the largest population

> 显示人口最多国家的人口密度

```sql
select name, population/area as density
from bbc
where population = (select max(population)
                    from bbc);  -- 子查询中现将最大的人口数的国家选出来
```

8. Pick the result that would be obtained from the following code

```sql
select region, sum(area)
from bbc
group by region
having sum(area) <= 20000000;
```

- 先求出每个`region`的人口总数
- 再把人口总数小于等于`2000000`的过滤掉

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gb11bcrrrwj30ey08q3z4.jpg)
