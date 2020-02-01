---
title: sqlzoo练习15-null-quiz
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-31 16:01:45
password:
summary:
tags:
  - sql
  - NULL
  - sqlzoo
categories:
  - MySQL
  - sqlzoo练习
---


### sqlzoo练习15-NULL quiz

主要是对上节中学习到的`NULL`空值的理解与复习

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbfndtn65qj30sw0oegnn.jpg)

[NULL-quiz](https://sqlzoo.net/wiki/Using_Null_Quiz)

<!--MORE-->

### 练习

1. select the code which uses an outer join correctly.

```sql
select teacher.name, dept.name
from teacher
left outer join dept on (teacher.dept=dept.id) -- 左连接
```

2. Select the correct statement that shows the name of department which employs Cutflower -

> 选出雇佣了Cutfolowerl老师的系

```sql
select dept.name
from teacher
join dept on (dept.id=teacher.dept)
where teacher.name='Cutflower';
```

3. Select out of following the code which uses a JOIN to show a list of all the departments and number of employed teachers

> 选出所有的院系和该院系雇佣的老师的数目

```sql
select dept.name, count(teacher.name)  -- 统计老师的个数
from teacher
right join dept on dept.id=teacher.dept
group by dept.name
```

4. Using `SELECT name, dept, COALESCE(dept, 0) AS result FROM teacher` on `teacher` table will:

```sql
display 0 in result column for all teachers without department
```

5. the result of Query

> 主要考察的是`case`语句的用法

```sql
select name,
     case when phone=2752 then 'two'
          when phone=2753 then 'three'
          when phone=2754 then 'four'
     end as digit
from teacher
```



> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbfsti79j0j30oi0ocq52.jpg)

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbfstvorukj30fm0bq40x.jpg)

6. Select the result that would be obtained from the following code:

```sql
select name,
    case when dept in (1)
         then 'Computing'  -- 如果dept编号是1，则标记为computing；否则是Other
    else 'Other' end
from teacher;
```

>![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbfsxpkjtkj30ca0bgwf8.jpg)
