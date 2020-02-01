---
title: sqlzoo练习3
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-13 17:54:20
password:
summary:
tags:
  - sql
categories:
  - MySQL
  - sqlzoo练习
---

Sqlzoo 练习3

We continue practicing simple SQL queries on a single table.This tutorial is concerned with a table of Nobel prize winners:

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gav1hnc1waj30gw0ai3z9.jpg)

<!--MORE-->

#### select子句顺序

1. select
2. from
3. where
4. group by
5. having
6. order by（desc是降序）

### 练习

1. Change the query shown so that it displays Nobel prizes for 1950.

```sql
SELECT yr, subject, winner
  FROM nobel
 WHERE yr = 1950;
```

2. Show who won the 1962 prize for Literature.

```sql
SELECT winner
FROM nobel
WHERE yr = 1962
AND subject = 'Literature';
```

3. Show the year and subject that won 'Albert Einstein' his prize.

```sql
select yr, subject 
from nobel
where winner = 'Albert Einstein';
```

4. Give the name of the 'Peace' winners since the year 2000, including 2000.

```sql
select winner
from nobel
where subject = 'Peace'
and yr >= 2000;
```

5. Show all details (**yr**, **subject**, **winner**) of the Literature prize winners for 1980 to 1989 inclusive.

```sql
select yr, subject, winner 
from nobel
where subject='Literature' 
and (yr between 1980 and 1989);
```

6. Show all details of the presidential winners:Theodore Roosevelt、Woodrow Wilson、Jimmy Carter、Barack Obama

```sql
select * 
from nobel
where winner in ('Theodore Roosevelt','Woodrow Wilson','Jimmy Carter','Barack Obama');
```

7. Show the winners with first name John

```sql
select winner
from nobel
where winner like 'John%';   -- 使用单引号
```

8. Show the year, subject, and name of Physics winners for 1980 together with the Chemistry winners for 1984.

```sql
select yr, subject, winner 
from nobel
where (yr = 1980 and subject = 'Physics')
or (yr = 1984 and subject = 'Chemistry');
```

9. Show the year, subject, and name of winners for 1980 excluding Chemistry and Medicine

```sql
select yr, subject, winner
from nobel
where yr = 1980 
and subject not in ('Chemistry', 'Medicine');
```

10. Show year, subject, and name of people who won a 'Medicine' prize in an early year (before 1910, not including 1910) together with winners of a 'Literature' prize in a later year (after 2004, including 2004)

```sql
select yr, subject, winner
from nobel
where (subject = 'Medicine' and yr < 1910) 
or (subject = 'Literature' and yr >= 2004);
```

11.Find all details of the prize won by PETER GRÜNBERG

```sql
select yr, subject, winner
from nobel
where winner like 'PETER%erg';  -- 使用通配符来解决
```

12. Find all details of the prize won by EUGENE O'NEILL

```sql
select yr, subject, winner
from nobel
where winner like 'EU%ILL'
```

13. **List the winners, year and subject where the winner starts with** **Sir**. Show the the most recent first, then by name order.

```sql
select winner,yr, subject
from nobel
where winner like 'Sir%'
order by yr desc, winner asc;  -- 指定时间的降序，名字的升序
```

14. The expression **subject IN ('Chemistry','Physics')** can be used as a value - it will be **0** or **1**：满足条件是1，否则是0

    Show the 1984 winners and subject **ordered by subject and winner name**; but list Chemistry and Physics last.

- 1984年获奖
- 学科与名字排序
- 化学和物理最后排序（满足条件为1，排在后面）

```sql
SELECT winner, subject
FROM nobel
WHERE yr=1984
ORDER BY subject IN ('Physics','Chemistry'),subject,winner
```

```sql
select winner, subject
from nobel
where yr=1984
order by 
case when subject in ('Physics','Chemistry') then 1 else 0 end,   -- 满足when语句为1，否则是0
subject, winner;
```
