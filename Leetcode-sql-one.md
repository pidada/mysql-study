---
title: Leetcode-sql-one
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-02-07 18:09:15
password:
summary:
tags:
  - sql
  - Leetcode
categories:
  - MySQL
  - 进阶
---

### LeetCode-SQL-one

本文中主要是介绍`LeetCode`中关于`SQL`的练习题，从易到难，循序渐进。文中会介绍题目和尽可能多的解答方案

- 组合两个表：通过`join`进行联结
- 第二高的薪水：`ifnull`和`limit`的用法以及建立临时表
- 超过经理收入的员工：一个表的自连接实现
- 从不订购的客户：两个表的连接

**关于SQL连接的问题解决，牢记下图**

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbnzwc3g1dj313z0u0q70.jpg)

<!--MORE-->

### 175-组合两个表

#### 题目

有Person和Address两个表，编写SQL语句，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：

```sql
FirstName, LastName, City, State
```

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbo016m4odj30ku0p4q47.jpg)



#### 答案

```sql
select FirstName, LastName, City, State
from Person
left join Address on   Person.PersonID=Address.PersonID;  -- left join 会保留左表的所记录，右表中不存在的字段则表示NULL
```



### 176-第二高的薪水

#### 题目

编写一个`SQL` 查询，获取 `Employee` 表中第二高的薪水（Salary）；如果没有，则表示为`NULL`

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbgzioitpoj309a08i74h.jpg)

#### 答案

1. 将不同的薪水按照降序排序
2. 使用`limit`子句来获得第二高的薪水
3. 如果没有第二高的薪水，使用`IFNULL`函数来解决

```sql
select ifnull((select distinct Salary   -- 某些员工的薪水可能相同，去重功能
               from Employee
               order by Salary desc   -- 降序排列
               limit 1 offset 1), null)   -- limit 1 offset 1 等价于limit 2, 1
        as SecondHighestSalary
```



```sql
select (select distinct Salary   -- ()里面的select语句是建立临时表，解决只有一条记录的问题
        from Employee
        order by Salary desc
        limit 1 offset 1)
  as SecondHighestSalary
```



```sql
select max(Salary) SecondHighestSalary
from Employee
where Salary < (select max(Salary) from Employee);   -- where语句是先去掉最高的，再从剩下的当中寻求最高的，即第二高
```



#### limit的用法

1. limit x：读取x条数据
2. limit x,y：从x开始，读取y条数据
3. limit y offset x：从x开始，读取y条数据

### 181-超过经理收入的员工

`Employee` 表包含所有员工，他们的经理也属于员工。每个员工都有一个 Id，此外还有一列对应员工的经理的 Id。

给定 `Employee` 表，编写一个 SQL 查询，该查询可以获取收入超过他们经理的员工的姓名。在上面的表格中，Joe 是唯一一个收入超过他的经理的员工。

>![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbh0i9a1aaj30gy09mmxv.jpg)

#### 答案

通过同一个表的自连接和`where`语句的判断来实现

```sql
select a.Name Employee
from Employee a
join Employee b on a.ManagerID=b.Id
where a.Salary > b.Salary;
```

通过where语句来实现

```sql
select a.Name Employee
from
    Employee a,
    Employee b
where a.ManagerID=b.Id
and a.Salary > b.Salary
```

### 182-查找重复的电子邮箱

#### 题目

编写一个 SQL 查询，查找 `Person` 表中所有重复的电子邮箱。

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbnxdnjh5kj30ai0840t0.jpg)

输出结果为：

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbnxe33pwcj308y062t8n.jpg)

#### 答案

子句顺序：`where>group by>having>order by`

```sql
-- 自己的答案
select distinct(Email)   -- 去重
from Person a,
     Person b
 where a.Email=b.Email   -- 过滤条件
 and a.id != b.id;
```

**最好的方法**

- `group by`进行汇总
- `having`进行行过滤
- `count(Email)`进行统计汇总数据，大于1则为重复

```sql
select Email
from Person
group by Email  -- 分组
having count(Email) > 1;  -- 过滤
```

```sql
select distinct(p1.Email)
from Person a
join Person b on a.Email=b.Email
and a.id != b.id;   -- 和自己的类似，将where条件换成了join连接
```

**使用临时表解决**

```sql
select Email
from (
  select Email, count(Email) as num
  from Person
  group by Email
) as statistic   -- 建立临时表，必须要有
where num > 1;
```

### 183-从不订购的客户

#### 题目

某网站包含两个表，`Customers` 表和 `Orders` 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户。

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbnz0ldthaj30kk0liab0.jpg)

输出结果为

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbnz12zjl4j308c07awek.jpg)

#### 答案

```sql
-- 自己答案
select Name as 'Customers'
from Customers
where Customers.Id not in
(select CustomerId
from Orders
join Customers on Orders.CustomerId=Customers.Id);
```

```sql
-- 官方答案
select customers.name as 'Customers'
from customers
where customers.id not in (
      select customerid from orders
)
```

```sql
-- 根据SQL连接来解决：查找不在某个表中的数据，在a中，但是不在b中

select a.Name as Customers
from Customers a
left join Orders as b
on a.Id=b.CustomerId
where b.CustomerId is null;   -- b中是空的；不能用 =null
```

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbnzqzhxikj313z0u0q9r.jpg)



