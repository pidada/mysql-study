---
title: Leetcode-sql-three
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-02-17 17:02:42
password:
summary:
tags:
  - sql
  - leetcode
categories:
  - MySQL
  - 进阶
---
### LeetCode-SQL-six

本文中主要是介绍`LeetCode`中关于`SQL`的练习题，从易到难，循序渐进。文中会介绍题目和提供尽可能多的解答方案。从本文开始属于$\color{red}{中等}$难度

<!--MORE-->

### 177-第N高的薪水

#### 题目

编写一个 SQL 查询，获取 `Employee` 表中第 *n* 高的薪水（Salary）

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbyf9z7z9xj309m08kdg1.jpg)

例如上述 `Employee` 表，*n = 2* 时，应返回第二高的薪水 `200`。如果不存在第 *n* 高的薪水，那么查询应返回 `null`

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbyfaj004hj30d2066gls.jpg)

#### 答案

`limit m,n` 相当于是`limit n offset m`

1. 查询薪水，降序排列，同时进行去重

```sql
select distinct salary
from Employee
order by Salary desc
limit 1,1;  -- 去重之后的第二条记录就是第二高的
```

2. 通过`ifnull`函数判断是否为空值

```sql
select ifnull((select distinct salary   -- 方法1
             from Employee
             order by Salary desc
             limit 1,1),null
             ) as SecondHighestSalary;
```

```sql
-- 方法2
create function getHighestSalary(N INT) returns INT
begin
		if N<0 then
		  return (select min(Salary) from Employee);
		else
		  set N=N-1;  -- limit的索引是从0开始的，比如想知道第2高，必须是limit 1,1(limit 2-1,1)
		  return (
        select ifnull(
          (select distinct Salary
                       from Employee
                       order by Salary desc limit N,1),null) as NthHighestSalary
      );
      end if;
    END
```

```sql
-- 方法3
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
    declare p int;   -- 变量声明
    set p=n-1;  -- 变量赋值
    return(
        select ifnull(
            (
                select distinct salary from employee
                order by salary desc limit p,1
            ),null
        ) as SecondHighestSalary  -- 关键的SQL查询语句，注意变量p
    );
    end
```

### 178-分数排名

#### 题目

编写一个 SQL 查询来实现分数排名。如果两个分数相同，则两个分数排名（Rank）相同。请注意，平分后的下一个名次应该是下一个连续的整数值。换句话说，名次之间不应该有“间隔”

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbygd05y0uj30880bkgm0.jpg)

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbygdd2o1nj30880b8dg8.jpg)

#### 答案

最终的结果包含两个部分：

1. 降序排列的分数
2. 每个分数对应的排名

**第一部分的查询语句为：**

```sql
select a.Score as score
from Scores a
order by a.Score desc;  -- 直接降序排列
```

**第二部分的分析过程：**

假设给定了某个分数`X`，如何计算它的排名`rank`，分为两个步骤：

- 先提取所有的**大于等于**X分数的集合H
- 将`H`**去重后的元素个数**就是`X`的排名

> 看个栗子：`99，98，98，97，97，97`，现在想知道97的排名，去重之后的元素排序为`99,98,97`，个数为`3`，所以`97`的排名为`3`

```sql
select b.Score from Scores b where b.Score >= X   -- 提取集合X
```

```sql
select count(distinct b.Score) from Scores b where b.Score >= X as rank;  -- 去重后的元素个数作为排名
```

结果中rank对应的是第一部分的分数，所以X就是a.Score，两个部分合在一起为：

```sql
select a.Score as Score,
      (select count(distinct b.Score)
       from Scores b
       where b.Score >= a.Score)
from Scores a
order by a.Score desc;
```

### 图解SQL排名

#### 题目

下面的班级表记录了每个学生所在的班级和成绩。

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbzdf37u4sj30hl0d3q3k.jpg)

现在需要按成绩来排名，如果两个分数相同，那么排名要是并列的。

> 正常排名是1，2，3，4，但是现在前3名是并列的名次，排名结果是：1，1，1，2。

#### 解题

如果涉及到排名问题，可以使用$\color{red}{窗口函数}$，3个函数为：

- rank
- dense_rank
- row_number

```sql
select *,
   rank() over (order by 成绩 desc) as ranking,
   dense_rank() over (order by 成绩 desc) as dese_rank,
   row_number() over (order by 成绩 desc) as row_num
from 班级
```

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbzdjr216nj30hn0d8abl.jpg)

**3个函数的具体区别：**

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbzdko07jej30ho0d80ts.jpg)

> MySQL 8开始才支持窗口函数
