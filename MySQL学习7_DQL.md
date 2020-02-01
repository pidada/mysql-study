---
title: MySQL学习7_DQL
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-04 15:18:33
password:
summary:
copyright: true
tags:
  - 数据库
  - MySQL
  - 数据查询语言
categories:
  - MySQL
  - 基础
---

### DQL

> `DQL（data query language）`数据查询语言，主要的作用是对数据库中的数据进行查询的操作，也是最常见和最重要的功能，查询的方法也是多种多样：`联合查询、分组查询、内连查询、子查询`等，还可以`限制查询的条数`等，下面介绍几种常见的查询

格式：

```sql
select column1,column2,...  # 需要查询的字段 
from table_name   # 表名
where 条件
```

<!--MORE-->

### 简单查询

```sql
select name,age 
from user
where id=4;   
where id in(1,3,5,7);
where name = "xiaoming";
```

### 过滤查询

过滤查询的关键字是`distinct`，去掉字段中的重复值

```sql
-- 过滤重复字段
select distinct(password) from user;     # password是重复项
select distinct password from user;   # 括号可以不用
```

![](https://s2.ax1x.com/2019/10/04/uDNPdP.png)

### 连接查询

连接查询的关键字是`concat`

- 直接使用系统默认的连接方式，将原来的字段通过下划线进行连接
- 使用`concat...as...`，`as`后面自己指定连接的新字段名
- 带上连接符号的查询`concat_ws("+", 列名1，列名2)`；其中"=="就是指定连接符

```sql
select concat(name, email) from user;          # 结果中显示concat(name_email)
select concat(name, email) as nameEmail from user;  # 将新的字段名用nameEmail来表示
```

![](https://s2.ax1x.com/2019/10/04/uDNeMj.png)

![](https://s2.ax1x.com/2019/10/04/uDNrWD.png)

![](https://s2.ax1x.com/2019/10/04/uDN5Y8.png)

#### 模糊查询

模糊查询的关键字是`like`，中文翻译成`像`：

```csharp
mysql> select user_name from student where user_name like "peter";    # 像peter
mysql> select user_name from student where user_name like "%e";      # %表示任意，表示名字以e结尾
mysql> select user_name from student where user_name like "%e%";    # 表示名字中含有e 
```

#### 排序查询

对表中的记录进行升序`asc`或者降序`desc`的排列，默认的是升序`asc`，同时需要使用`order by`关键字：

- 升序：`asc`，默认情况
- 降序：`desc`

```sql
select * from student order by user_age asc;   # 年龄的升序
select * from student order by user_age desc;  # 年龄的降序
```

![](https://s2.ax1x.com/2019/10/04/uDUATx.png)

#### 聚合函数

```sql
select count(*) from student;    # 总记录
select sum(列名) from student;  # 总和
select avg(列名) from student;   # 平均值
select max/min(列名) from student;  # 最大/小值
```

### 限制查询结果

限制查询的条数使用的是`limit`关键字

- 直接使用`limit`
- 使用`limit ... offset ...`：指定从哪里开始显示，显示多少行
- 简写：`limit 5, 4`：表示从第5行开始，显示4行数据

```sql
select name, age from user limit 5;    -- 只显示5行数据
select name, age from user limit 5 offset 4;   -- 从第4（offset）行开始显示5(limit)行数据 
select name, age from user limit 4, 5 ;  -- 效果同上：逗号之前是offset的内容，逗号之后是limit内容
```

