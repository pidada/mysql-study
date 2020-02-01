---
title: sqlzoo练习5
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-15 00:04:46
password:
summary:
tags:
  - sql
  - 子查询
categories:
  - MySQL
  - sqlzoo练习
---

sqlzoo练习6-select in select

本文中主要介绍的是子查询`select in select`

> This tutorial looks at how we can use SELECT statements within SELECT statements to perform more complex queries.

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gawf3wv4gwj30ow0bewfz.jpg)

<!--MORE-->

1. **List each country** **name** **where the** **population** **is larger than that of 'Russia'.**

> 找出人口比俄罗斯多的国家

```sql
select name from world
where population > (select population  -- 找出人口大于Russia
                    from world
                    where name='Russia');
```

2. **Show the countries in Europe with a per capita GDP greater than 'United Kingdom'.**

> 找出人均gdp比UK大的国家

```sql
select name
from world
where gdp/population > (select gdp/population
                        from world
                       where name='United  Kingdom')
and contient='Europe';
```

3. **List the** **name** **and** **continent** **of countries in the continents containing either** **Argentina** **or** **Australia** .**Order by name of the country.**

- 找出和**Argentina** **or** **Australia** 相同洲的国家

```sql
select name, continent
from world
where continent in (select continent from world
                    where name='Argentina' or name='Australia')   -- 选择出两个国家所在的洲
order by name;
```

4. **Which country has a population that is more than Canada but less than Poland? Show the name and the population.**

```sql
select name,population
from world
where population > (select population from world where name='Canada')
and population < (select population from world where name='Poland');
```

5. **Show the name and the population of each country in Europe. Show the population as a percentage of the population of Germany.**

> Germany (population 80 million) has the largest population of the countries in Europe. Austria (population 8.5 million) has 11% of the population of Germany.

- 找出欧洲所有国家name
- 人口`population`以**和德国的百分比表示**

```sql
select name, concat(round(100*population / (select population from world where name='Germany')), '%')
from world
where continent='Europe';
```

> 笔记：换成百分比必须加上100*

1. round函数是去掉小数位
2. concat函数是指定连接符号，必须使用单引号将`%`抱起来

> 关于all关键字的使用：通过符号`\>= or > or < or <=`来表示大小关系。通过一个demo来表示

```sql
 SELECT name
 FROM world
 WHERE population >= ALL(SELECT population   -- 找出人口最多的国家
                           FROM world
                          WHERE population>0)
```

6. **Which countries have a GDP greater than every country in Europe? [Give the** **name** **only.] (Some countries may have NULL gdp values)**

> 哪些国家的GDP比欧洲每个国家的GDP都要大

```sql
select name
from world
where gdp > all(select gdp from world where  continent='Europe' and gdp >0);    --all语句是找出Europe中的全部gdp
```

7. **Find the largest country (by area) in each continent, show the** **continent,the name and the area**

- 找出每个洲中面积最大的国家

```sql
select continent, name, area
from world x
where area >= all(select area
                  from world y
                 where x.continent=y.continent
                 and area>0)
```

8. **List each continent and the name of the country that comes first alphabetically.**

> 找出所有的洲，按照国家的字母顺序排序

```sql
select continent,name
from world x
where x.name <= all(select name from world y where x.continent=y.continent);
```

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gawibm9d9oj30ia0imabr.jpg)

9. **Find the continents where all countries have a population <= 25000000. Then find the names of the countries associated with these continents. Show** **nameontinent  and population**

> 找出洲中全部国家的人口都小于25000000的那些洲

```sql
select name, continent, population
from world x
where 25000000 >= ALL(select population from world y where x.continent=y.continent and population>0)

-- case语句
select name, continent, population
from world x
where not exists (select * from world y   -- 都小于25000000表明不存在大于25000000
                 where x.continent=y.continent   -- 同一个洲
                 and x.population > 25000000  -- 大于25000000
                 );
```

10. **Some countries have populations more than three times that of any of their neighbours (in the same continent). Give the countries and continents.**

> 找出在同一个洲中超出其他任意一个国家人口3倍的那些国家

```sql
select x.name, x.continent -- 指定从哪个表中查找的数据
from world x
where x.population/3 >all(select population from world y
                           where x.continent=y.continent  -- 洲相同
                           and x.name != y.name  -- 名字不能相同
                         );
```


