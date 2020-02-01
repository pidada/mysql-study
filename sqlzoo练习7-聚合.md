---
title: sqlzoo练习7-聚合
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-15 23:16:38
password:
summary:
tags:
  - sqlzoo
  - sql
  - 聚合
categories:
  - MySQL
  - sqlzoo练习
---

### sqlzoo练习7

主要涉及到的知识点是**聚合函数**：`sum and count`等

> This tutorial is about aggregate functions such as COUNT, SUM and AVG. An aggregate function takes many values and delivers just one value. For example the function SUM would aggregate the values 2, 4 and 5 to deliver the single value 11

- sum求和
- count统计数量
- max、min 求出最值
- distinct去重功能

 <!--MORE-->

> ![image-20200115180124730](https://tva1.sinaimg.cn/large/006tNbRwgy1gaxei76h98j30qq0cw406.jpg)

### 练习

1. Show the total **population** of the world.

```sql
select sum(population) from world;
```



2. List all the continents - just once each.

> 列出每个不同的洲，使用distinct去重

```sql
select distinct(continent) from world;
```

3. Give the total GDP of Africa

> 计算非洲的总gdp，sum求和

```sql
select sum(gdp) from world where continent='Africa';
```

4. How many countries have an **area** of at least 1000000

> 统计count多少个国家的面积大于1000000

```sql
select count(name) from world
 where area > 1000000;
```

5. What is the total **population** of ('Estonia', 'Latvia', 'Lithuania')

> 3个国家的总人口sum

```sql
select sum(population) from world
where name in ('Estonia', 'Latvia', 'Lithuania');
```

6. For each **continent** show the **continent** and number of countries.

> 每个地区continent有多少个国家count

```sql
select continent, count(name)  -- 统计总数
from world
group by continent;  -- 地区分组
```

7. For each **continent** show the **continent** and number of countries with populations of at least 10 million.

> 加上where条件再进行分组

```sql
select continent, count(name)
from world
where population >= 10000000  -- 先用where进行筛选
group by continent;
```

8. List the continents that **have** a total population of at least 100 million.

> having是对分组之后的结果进行筛选

```sql
select continent
from world
group by continent  -- 先分组再进行筛选
having sum(population) >= 100000000;
```



### Group By and Having

#### select子句顺序

1. select
2. from
3. where
4. group by
5. having
6. order by

#### 习题

1. For each continent show the number of countries

> 统计每个洲中国家的数量
>
> - 洲分组
> - 统计数量count

```sql
select continent, count(name)
from world
group by continent;  -- 分组
```

2. For each continent show the total population

> 统计每个洲的人口总数
>
> - 洲分组
> - 统计总数sum

```sql
select continent, sum(population)  -- 人口求和
from world
group by continent;
```

3. For each relevant continent show the number of countries that has a population of at least 200000000.

> where语句在group by之前
>
> - 每个地区的国家总数
> - 人口需要大于20000000

```sql
select continent,count(name)
from world
where population > 20000000
group by continent;
```

4. Show the total population of those continents with a total population of at least half a billion.

> The HAVING clause is tested **after the GROUP BY**

```sql
select continent, sum(population)   -- 统计总人口
from world
group by continent
having  sum(population) > 500000000;   -- 总人口满足的条件
```


