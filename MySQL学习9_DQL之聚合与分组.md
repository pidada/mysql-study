---
title: MySQL学习9_DQL之聚合与分组
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-05 08:58:51
password:
summary:
copyright: true
tags:
  - 聚合函数
  - group by
  - having
  - 数据查询语言
categories:
  - MySQL
  - 基础
---

### 聚合函数

在实际中我们可能只是需要汇总数据而不是将它们检索出来，`SQL`提供了专门的函数来使用。聚合函数`aggregate function`具有特定的使用场景

#### 使用场景

- 确定表中的行数（或者满足某个条件或者包含某个特定值的行数）
- 获取数据中某些行的和
- 找出表中（特定行或者所有行）的`max、min、mean、sum`等

### select子句顺序

`select`子句在是用的时候必须遵循的顺序是

1. select
2. from
3. where：行级过滤
4. group by：分组说明
5. having：组级过滤
6. order by：输出排序顺序

<!--MORE-->

#### 常见的聚合函数

- AVG()：平均值，自动忽略值为NULL的行
- COUNT()：行数
  - count(*)：统计所有行，`包含空行`
  - count(column)：对特定列column中具有值的行进行计数，`忽略空行`
- MAX(column)：最大值，一般是用来找最大的数值或者日期
  - 指定列名
  - 自动忽略空行
  - 用于文本数据返回的是排序后的`最后一行`
- MIN()：最小值
  - 指定列名，自动忽略空行
  - 文本数据：返回排列后的`第一行`
- SUM()：总和
  - 可以用于合计计算
  - 自动忽略空行

#### 栗子

聚合函数使用的格式为

```sql
select AVG/MIN/MAX(字段名) as other_name from table_name   -- as后面表示用于显示的别名
```

```sql
select AVG(prod_price) as avg_price from Products where vend_id = 'DDL01';   -- 返回的是指定行DDL01的平均值

select count(cust_email) as num_cust from customers;    -- 指定列cust_email
select max(prod_price) as max_price from Products;
select min(prod_price) as max_price from Products;

-- 顺序：select--->from--->order by--->where
select sum(item_price * quantity) as total_price   -- 用于合计计算
from OrderItems
where order_num = 20005;
```

#### 聚集不同值

上面5个聚合函数的参数

- 对所有的行执行：`all`，默认行为
- 去重后执行操作：`distinct`，必须指定列名

```sql
select avg(distinct prod_price) as avg_price   -- 将属性prod_price中相同的值去掉
from prorducts 
where vend_id = 'DLL01';
```

#### 组合聚集函数

- 同时指定多个聚合函数
- `as`后面跟的列名最好不是原表中已经存在的列名，避免歧义

```sql
select count(*) as num_items,  -- 同时使用多个聚合函数
min(prod_price) as price_min,
max(prod_price) as price_max,
avg(prod_price) as avg_price
from Products
where vend_id = 'DLL01';
```



### 分组

使用分组可以将数据多个逻辑组，对每个组进行聚集计算。分组中使用最多的是`group by`和`having`

#### group by

看一个`group by`的栗子

```sql
select vend_id, coutn(*) as num_prods  -- 指定两个列
from Products
group  by vend_id;  -- 指定分组的字段：对每个vend_id进行计算
```

规定：

- `group by`中可以包含任意数目的列，可以进行嵌套
- `group by`子句中列出的每一列都是检索列或者有效的表达式
- `group by`在`where`之后，`order by`之前
- 能够通过相对位置指定列，`group by 2, 1`
- 如果分组列中带有`NULL`的行，将它们作为一个组返回

#### having

> 除了能够`group by`进行分组，还可以进行过滤分组。规定包含哪些分组，排除哪些分组。`having`应该结合`group by`子句一起使用。

几乎所有的`where`语句都可以用`having`语句来代替。二者区别：

- `where`过滤的是行，在`数据分组之前`进行过滤
- `having`过滤的是分组，在`数组分组之后`进行过滤

```sql
select cust_id, count(*) as orders
from Orders 
group by cust_id       -- 先实现行分组
having count(*) >= 2;  --在上面的结果基础上选取大于等于2的行
```

**where和having同时使用**

```sql
select vend_id, count(*) as num_prods  -- 选择供应商，产品总数
from Products  
where prod_price >= 4  -- 产品价格的条件
group by vend_id   -- 通过供应商id来分组
having count(*) >= 2;  -- 选择总数大于等于2


/*result
1. 显示的字段是select语句后面指定的
2. num_prods >= 2
3. 通过vend_id进行分组
*/
ven_id   num_prods
------   --------
BRS01        3
FNG02        2
```

### order by和group by

| order by         | group by                                                   |
| ---------------- | ---------------------------------------------------------- |
| 对产生的输出排序 | 对行进行分组，输出可能不是分组的顺序                       |
| 任意列均可使用   | 只可能使用选择列或者列表达式，而且必须使用每个选择列表达式 |
| 句中未必需要     | 如果有聚集函数，必须使用                                   |

```sql
select order_num, count(*) as items
from OrderItems
group by order_num
having count(*) >= 3;

-- 按照订购物品的数目进行排序输出
select order_num, count(*) as items
from OrderItems
group by order_num
having count(*) >= 3    -- 过滤数据
order by items, order_num; -- 作用：对上面的结果按items(商品数目)排序输出；若数目相同，再按照order_num
```