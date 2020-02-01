---
title: sqlzoo练习14-using-null
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-31 15:05:33
password:
summary:
tags:
  - sqlzoo
  - NULL
categories:
  - MySQL
  - sqlzoo练习
---


### sqlzoo练习14-using NULL

本文中讲解的是当数据库的表中的数据存在缺失值`NULL`的时候，该如何进行处理。下面的数据含有两个表`teacher`和`dept`

> The school includes many departments. Most teachers work exclusively for a single department. Some teachers have no department.

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbfndtn65qj30sw0oegnn.jpg)

[using null](https://sqlzoo.net/wiki/Using_Null)

<!--MORE-->

### 练习

1. List the teachers who have NULL for their department.

> 找出dept为空的老师

```sql
select name
from teacher
where dept in NULL;   -- 注意不能使用dept=NULL
```



2. Note the INNER JOIN misses the teachers with no department and the departments with no teacher.

> inner join能够排除老师为空的系或者没有系的老师

```sql
select teacher.name, dept.name
from teacher
inner join dept on (teacher.dept=dept.id);
```

3. Use a different JOIN so that all teachers are listed.

```sql
select teacher.name. dept.name
from teacher
full join dept on teacher.dept=dept.id
where teacher.name is not null;  -- 方法1
```

```sql
select teacher.name, dept.name
from teacher
left join dept on teacher.dept=detp.id;  -- 方法2
```

4. Use a different JOIN so that all departments are listed.

```sql
-- 方法1
select teacher.name, dept.name
from dept
full join teacher on dept.id=teacher.dept
where dept.name is not null;

-- 方法2
select teacher.name, dept.name
from teacher
right join dept on teacher.dept=detp.id;
```

5. Use COALESCE to print the mobile number. Use the number '07986 444 2266' if there is no number given. **Show teacher name and mobile number or '07986 444 2266'**

```sql
select name, coalesce(mobile, '07986 444 2266')
from teacher
```

6. Use the COALESCE function and a LEFT JOIN to print the teacher **name** and department name. Use the string 'None' where there is no department.

```sql
select teacher.name, coalesce(dept.name, 'None')
from teacher
left join dept on teacher.dept=dept.id;
```

7. Use COUNT to show the number of teachers and the number of mobile phones.

```sql
select count(name), count(mobile)
from teacher;
```

8. Use COUNT and GROUP BY **dept.name** to show each department and the number of staff.  Use a RIGHT JOIN to ensure that the Engineering department is listed.

```sql
select dept.name, count(teacher.name)
from teacher
right join dept on dept.id=teacher.dept
group by dept.name;
```

9. Use CASE to show the **name** of each teacher followed by 'Sci' if the teacher is in **dept** 1 or 2 and 'Art' otherwise.

```sql
select teacher.name,
   case when (teacher.dept=1 or teacher.dept=2) then 'Sci'
   else 'Art' end
from teacher;
```



10. Use CASE to show the name of each teacher followed by 'Sci' if the  teacher is in dept 1 or 2, show 'Art' if the teacher's dept is 3 and  'None' otherwise.

```sql
select teacher.name,
  case when teacher.dept=1 or teacher.dept=2 then 'Sci'
       when teacher.dept=3 then 'Art'
  else 'None' end
from teacher
```



### About coalesce函数

COALESCE takes any number of arguments and returns the first value that is not null.

> 笔记：取第一个不是NULL值的数据

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbfnzd4yw0j30kk08875h.jpg)

### About case function

`case`表达式的两种写法

```sql
case sex   -- 1. 简单表达式
   when '1' then '男'
   when '2' then '女'
else 'other' end


case when sex='1' then '男'  -- 2. 搜索表达式
     when sex='2' then '女'
else 'other' end
```


