---
title: SQL进阶-8-集合运算
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-09-20 20:23:32
password:
summary:
tags:
  - 数据库
  - MySQL
categories:
  - MySQL
  - 进阶
---


### SQL进阶-8-利用SQL进行集合运算

**SQL本身就是以集合作为基础的**，本文中记录的是利用各种集合运算。

- 交集
- 并集
- 差集
- 异或集

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4nadc6dlj30bw0h4tcv.jpg)

<!--MORE-->

### 交集

内连接相当于是求两个集合的交集，相当于是集合的积（intersect）

```mysql
select_statment_1 intersect select_statment_2  -- 将两个语句直接连接

select name from Table1
intersect
select name from Table2

-- 如果不支持intersect方法，使用连接方法
select A.name as name
from tableA A
join tableB B
on A.name = B.name;   -- 通过相同的字段连接起来
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gix9ahwpobj30hu071ab9.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gix9apac8tj30gm073dgz.jpg)

### 并集

并集，其实就是全外连接的使用，相当于是求集合的和（union）

- Union  不包含重复的数据
- Union all 包含重复的数据

```mysql
-- 使用全连接使用

select
	coalesce(A.id, B.id) as id   -- coalesce()接收多个参数，返回的是第一个非NULL的参数
	,A.name as A_name
	,B.name as B_name
from Class_A A
full outer join Class_B B
on A.id = B.id;


-- 结果
id      A_name     B_name
1       田中        田中
2       铃木        铃木
3       伊集院
4                   田园寺


-- 使用左右连接+union合并实现（如果数据库不支持全外连接）

select
	A.id as id
	,A.name
	,B.name
from Class_A A
left outer join  Class_B B
on A.id = B.id
union select
				B.id as id
				,A.name
				,B.name
			from Class_A A
			right outer join Class_B B
			on A.id = B.id;
```

### 差集A-B

A中存在但是B中不存在

```mysql
select
	A.id as id
	,A.name as A_name
from class_A
left (outer) join class_B B
on A.id = B.id
where B.name is null;   -- B中不存在
```

差集的应用题目：

```mysql
select distinct shop  -- 用表items减去表shopitems里面每个商品，若结果为空，则说明店铺库存里面有items中的全部商品
from shopitems s1
where not exists   -- 不存在
	(select I.item
   from Items I
   left outer join shopitems s2
   on i.item = s2.item and s1.shop = s2.shop  -- 指定连接条件
   where s2.item is null
  );
```



![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gixdrdbg8oj307706zdgi.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gixdssjrrsj30eu0ijgom.jpg)



### 异或集（A并B)-(A交B)

异或指的是两个集合的并集除去它们的交集之外的集合：**并集-交集**

SQL中没有定义，可以通过两种方式是实现：

- (A union B) except (A intersect B)
- (A except B) union (B except A)

```mysql
select
	coalesce(A.id, B.id) as id
	,coalesce(A.name, B.name) as name
from Class_A A
full outer join Class_B B   --
on A.id = B.id
where (A.name is null
or B.name is null);   -- 或者or
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gixdjpoivej30oh07hwh1.jpg)
