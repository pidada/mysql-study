---
title: sqlzoo练习16-self-join
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-31 20:29:05
password:
summary:
tags:
  - sqlzoo
  - sql
  - self-join
categories:
  - MySQL
  - sqlzoo练习
---

### sqlzoo练习16-self-join

连接查询包含`3`种方式：

- 内联结
- 外联结
- 交叉联结

本文中学习的是自连接`self-join`。涉及到的两个表及其相关的字段

<!--MORE-->

```sql
stops(id, name)
route(num, company, pos, stop)
```


#### stops表

This is a list of areas served by buses. The detail does not really include each actual bus stop - just areas within Edinburgh and whole towns near Edinburgh.

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbg0nux0p1j30rm052jrw.jpg)

#### route表

A route is the path through town taken by a bus.

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbg0nxoqxvj31e408uwgs.jpg)

### 练习

**所有的题目运行都出问题，提示表是非法，不存在，why？**

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbg0mco3puj30tg0gsq49.jpg)

1. How many **stops** are in the database.

```sql
select count(stops.id)
from stops;
```

2. Find the **id** value for the stop 'Craiglockhart'

```sql
select id
from stops
where name='Craiglockhart';
```

3. Give the **id** and the **name** for the **stops** on the '4' 'LRT' service.

```sql
select id, name
from stops
join route on stops.id=route.stop
where num=4
and company='LRT';
```

4. The query shown gives the number of routes that visit either London  Road (149) or Craiglockhart (53). Run the query and notice the two  services that link these **stops** have a count of 2.

   **Add a HAVING clause to restrict the output to these two routes.**

> 笔记：
>
> 1. 显示了两条路线的总数
> 2. 分组和过滤
> 3. 要求：添加1个having子句来将结果限制成2条线路

```sql
select company, num, count(*)
from route
where stop=149 or stop=53
group by company, num
having count(*) = 2;  -- 添加语句，过滤结果
```

5. Execute the self join shown and observe that b.stop gives all the places you can get to from Craiglockhart, without changing routes.

   **Change the query so that it shows the services from Craiglockhart(53) to  London Road(149).**

```sql
select a.company, a.num, a.stop, b.stop
from route a
join route b on (a.company=b.company and a.num=b.num)
where a.stop=53
and b.stop=149;
```



---------

> **下面的部分需要重新做，参考了两个博主：**

[https://github.com/edsfocci/SQL-SQL_Zoo/blob/master/09_self_join.sql](https://github.com/edsfocci/SQL-SQL_Zoo/blob/master/09_self_join.sql)

[https://github.com/jisaw/sqlzoo-solutions/blob/master/self-join.sql](https://github.com/jisaw/sqlzoo-solutions/blob/master/self-join.sql)



6. The query shown is similar to the previous one, however by joining two copies of the **stops** table we can refer to **stops** by **name** rather than by number.

   **Change the query so that the services between 'Craiglockhart' and  'London Road' are shown. If you are tired of these places try  'Fairmilehead' against 'Tollcross'**

```sql
select a.company, a.num, stopa.name, stopb.name
from route a
join route b on (a.company=b.company and a.num=b.num)
join stops stopa on (a.stop=stopa.id)
join stops stopb on (b.stop=stopb.id)
where stopa.name='Craiglockhart'
and stopb.name='London Road';
```

7. Give a list of all the services which connect stops 115 and 137 ('Haymarket' and 'Leith')

> 列出连接两个站点的全部服务信息

```sql
select distinct a.company, a.num
from route a
    join route b on (a.num=b.num and a.company=b.company)
    join stops stopa on (a.stop=stopa.id)
    join stops stopb on (b.stop=stopb.id)
where a.stop=115
and b.stop=137;
```

8. Give a list of the services which connect the **stops** 'Craiglockhart' and 'Tollcross'

```sql
select distinct a.company, a.num
from route a
    join route b on (a.num=b.num and a.company=b.company)
    join stops stopa on (a.stop=stopa.id)
    join stops stopb on (b.stop=stopb.id)
where stopa.name='Craiglockhart'
and stopb.name='Tollcross';
```

9. Give a distinct list of the **stops** which may be reached from  'Craiglockhart' by taking one bus, including 'Craiglockhart' itself,  offered by the LRT company. Include the company and bus no. of the  relevant services.

```sql
select stopa.name, a.company, a.num
from route a
   join route b on (a.num=b.numa and a.company=b.company)
   join stops stopa on (a.stop=stopa.id)
   join stops stopa on (b.stop=stopb.id)
where stopb.name='Craiglockhart';
```

10. Find the routes involving two buses that can go from **Craiglockhart** to **Lochend**.Show the bus no. and company for the first bus, the name of the stop for the transfer, and the bus no. and company for the second bus.

```sql
select stopa.name, stopb.name
from route a
join route b on (a.num=b.num)
join stops stopa on (a.stop=stopa.id)
join stops stopb on (b.stop=stopb.id)
where stopa.name = 'Craiglockhart'
and stopb.name = 'Sighthill';
```

### self join quiz

1. Select the code that would show it is possible to get from Craiglockhart to Haymarket

```sql
select distinct a.name, b.name
from stops a 
   join route z on a.id=z.stop
   join route y on y.num=z.num
   join stops b on y.stop=b.id
where a.name='Craiglockhart' and b.name='Haymarket';
```

2.  Select the code that shows the stops that are on route.num '2A' which can be reached with one bus from Haymarket?

```sql
select s2.id, s2.name, r2.company, r2.num
from stops s1, stops s2, route r1, route r2
where s1.name='Haymarket' and s1.id=r1.stop
and r1.company=r2.company 
and r1.num=r2.num
and r2.stop=r2.id
and r2.num='2A';
```

3. Select the code that shows the services available from Tollcross?

```sql
select a.company, a.num, stopa.name, stopb.name
from route a
join route b on (a.company=b.company and a.num=b.num)
join stops stopa on (a.stop=b.stop)
join stops stopb on (b.stop=stopb.id)
where stopa.name='Tollcross';
```


### demo

油管上看到的一个例子，讲解如何通过不同的方式实现自连接。

- 内联结：`inner join`，忽略空行
- 外联结：`left/right/full join`，保留空行；
- 交叉联结：`cross join`，保留空行

**Oracle数据库支持full join，mysql是不支持full join的**

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbg0dks4v8j31f80qk1kx.jpg)
