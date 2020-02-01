---
title: sqlzoo练习6
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-15 17:55:14
password:
summary:
tags:
  - sqlzoo
  - 子查询
categories:
  - MySQL
  - sqlzoo练习
---

`select in select`部分的小测`quiz`，`5`个不同的字段信息

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gaxedgce5ij30v20hwtb1.jpg)

<!--MORE-->

### 习题

1. Select the code that shows the name, region and population of the smallest country in each region

> 每个地区人口最少的国家

```sql
select region, name, population
from bbc x
where population <= ALL(select population
                        from bbc y y.region=x.region
                        and population > 0);
```

相当于是把**同一个地区**中的人数进行对比，选择最少的那个

2.  Select the code that shows the countries belonging to regions with all populations over 50000

> 地区中每个国家的人数都超过了50000的地区region

```sql
select name, region, population
from bbc
where 50000 < ALL(select population
                  from bbc y
                  where x.region=y.region
                  and y.population>0);  -- 保证y中的populaton大于0
```

3. Select the code that shows the countries with a less than a third of the population of the countries around it

> 同一个地区中某个国家的人口小于其他国家的3分之一

```sql
select name, region
from bbc x
where population < ALL(select  population / 3
                       from bbc y
                       where x.region=y.region
                       and x.name != y.name)  -- 进行比较的两个国家名字不同
```

4. 根据代码选择答案

> 人口比英国多
>
> 和英国在同一个地区



```sql
select name from bbc
where population > (select population
                    from bbc
                    where name='United Kingdom')
and region in (select region
               from bbc
               where name='United Kingdom');
```

5. Select the code that would show the countries with a greater GDP than any country in Africa (some countries may have NULL gdp values).

> 比非洲所有的国家的gdp都要大；考虑有些国家没有gdp

```sql
select name from bbc
where gdp > (select max(gdp) from bbc where region='Africa');  -- 只需要比最大的gdp都要大即可
```

6. Select the code that shows the countries with population smaller than Russia but bigger than Denmark

> 人口比俄罗斯小，比Denmark大

```sql
select name from bbc
where population < (select population from bbc where name = 'Russia')
and population > (select population from bbc where name = 'Denmark');
```

7. 根据代码选答案

- 比欧洲所以国家的人口都要多
- 地区在南亚

```sql
select name from bbc
where population > all(select max(population)   -- 大于最大的人口即可
                       from bbc
                       where region='Europe')
and region='South Asia';
```


