---
title: Leetcode-sql-two
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-02-15 09:28:23
password:
summary:
tags:
  - udpate
  - if
  - case
categories:
  - MySQL
  - 进阶
---

### LeetCode-sql-two

本文主要是介绍`LeetCode`中关于`SQL`的练习题，从易到难，循序渐进。文中会介绍题目和尽可能多的解答方案

- 最大的国家：通过`where`条件进行查询，或者使用`union`进行联结

- 超过5名学生的课：`group by`分组和`having`过滤，注意使用`distinct`去重

- 有趣的电影：`where`条件使用，`SQL`中**奇偶数**的判断方法

- 交换性别：`case`语句和`if`语句的使用，

<!--MORE-->

### 595-找出最大的国家

#### 题目

如果一个国家的面积超过`300`万平方公里，或者人口超过`2500`万，那么这个国家就是大国家。

下图是`World`表，编写一个SQL查询，输出表中所有大国家的名称、人口和面积。

>![](https://tva1.sinaimg.cn/large/0082zybpgy1gbvrzkjtnaj30xo0k2gnt.jpg)

#### 答案

直接通过or来进行联结

```sql
select name, population, area   -- 方法1
from world
where (area > 3000000) or (population > 25000000);
```

通过union来进行联结

```sql
SELECT name, population, area FROM world   -- 方法2
WHERE area > 3000000
UNION
SELECT name, population, area FROM world
WHERE population > 25000000;
```

> 这道题就是这么的简单，做出来自己都不敢相信，采用的是方法1

### 596-超过5名学生的课

#### 题目

有一个`courses` 表 ，有: **student (学生)** 和 **class (课程)**。请列出所有超过或等于5名学生的课。

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbvqufu466j30ca0ekaaq.jpg)

结果应该为：

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbvqut8yicj306e05mjrd.jpg)

#### 答案

通过建立临时表来解决

```sql
select class
from (select class, count(distinct student) as num
      from courses
      group by class) as temp
where num >= 5;
```

通过`having`过滤来解决，注意使用`distinct`，有些可能是重复记录

```sql
select class
from courses
group by class
having count(distinct student) >= 5;
```

**一定要注意distinct**

### 620-有趣的电影

#### 题目

编写一个 SQL查询，找出所有影片描述为**非** `boring` (不无聊) 的并且 **id 为奇数** 的影片，结果请按等级 `rating` 排列。

>![](https://tva1.sinaimg.cn/large/0082zybpgy1gbvrocjgbxj30qy0netb2.jpg)

#### 答案

```sql
-- 自己解决
select id,movie,description,rating
from cinema
where description != 'boring'   -- <> 也表示不等于
and id%2 =1
order by rating desc;   -- 通过where的两个条件和排序解决
```

**奇偶数的判断方法**

- id&1
- id%2
- mod(id, 2) =1，表示对2求余数=1

### 627-交换工资

#### 题目

给定一个 salary 表，如下所示，有 **m = 男性** 和 **f = 女性** 的值。交换所有的 f 和 m 值（例如，将所有 f 值更改为 m，反之亦然）。要求只使用一个更新（Update）语句，并且没有中间的临时表。

**注意，您必只能写一个 Update 语句，请不要编写任何 Select 语句**

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbwsqqjz5ij30ke0i4abl.jpg)

#### 答案

首先需要了解更新语句的基本操作：

```python
update 表名
set 列名=修改后的值;
```



自己的解决方案中case语句略微复杂啦，可以进行简化，如同方法2

```sql
-- 方法1：自己方案
update salary
set sex=(
    case sex when 'm' then 'f'
             when 'f' then 'm'
             else 'other' end);
```

> ![](https://tva1.sinaimg.cn/large/0082zybpgy1gbwtonj554j30p009otcy.jpg)

```sql
-- 方法2：使用case表达式
update salary
set sex=(case sex when 'm' then 'f' else 'm' end);
```

```sql
-- 方法3：使用if语句
update salary set sex = if(sex='m','f','m');
```

#### if语句

 `if(expr1,expr2,expr3)`：

- 如果`expr1`的值为`true`，则返回`expr2`的值
- 如果`expr1`的值为`false`，则返回`expr3`的值。

**网上看到了一种最牛的做法：直接使用ASCII码进行转换**

```sql
update salary set sex=char(ascii('m') + ascii('f') - ascii(sex));
```


