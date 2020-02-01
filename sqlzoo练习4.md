---
title: sqlzoo练习4
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-14 18:35:09
password:
summary:
tags:
  - sql
categories:
  - MySQL
  - sqlzoo练习
---

Nobel Quiz

下面是`Nobel`表中字段的相关信息

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gaw9disjglj30nk0ig761.jpg)



<!--MORE-->

### 题目

1. Pick the code which shows the name of winner's names beginning with C and ending in n

> 通配符的使用

```sql
select name
from nobel
where winner like 'C%'' and winner like '%n';
```



2. Select the code that shows how many Chemistry awards were given between 1950 and 1960

> 找出两个年份之间化学奖品被授予的次数

```sql
select count(subject)   -- 统计总数
from nobel
where subject = 'Chemistry'
and yr between 1950 and 1960;
```

3. Pick the code that shows the amount of years where no Medicine awards were given

> 统计没有医药授予的年份，需要考虑去重

```sql
select count(distinct yr)
from nobel
where yr not in (select distinct yr from nobel where subject = 'Medicine');   -- 现将Medicine的年份选出来，然后排除掉
```

4. 从代码中选正确结果

```sql
SELECT subject, winner FROM nobel WHERE winner LIKE 'Sir%' AND yr LIKE '196%'
```

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gaw9d92o3mj30lo0520t4.jpg)

5. Select the code which would show the year when neither a Physics or Chemistry award was given

> 物理和化学同时不授予的年份

```sql
select yr from nobel
where yr not in (select yr from nobel
                where subject in ('Chemistry', 'Physics'))  -- 先通过子查询选择出两个学科的年份，然后排除掉这些年份
```

6. Select the code which shows the years when a Medicine award was given but no Peace or Literature award was

> 找出医药授予，但是和平和文学不授予的年份

```sql
select distinct yr from nobel
where subject = 'Medicine'
and yr not in (select yr from nobel where subject='Literature')  -- 把两个学科的年份同时排除掉
and yr not in (select yr from nobel where subject='Peace');
```

7. 从代码中选出正确的答案

select subject, count(subject)  -- 统计每个学科的数量
from nobel
where yr='1960'
group by subject;

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gaw9mt91gwj30di0deq3j.jpg)
