---
title: MySQL学习8_排序与过滤
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-04 16:34:27
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

### 常见语句顺序

在`SQL`查询中的常见语句顺序是

- `select`
- `from`
- `where`
- `order by`

------

使用的数据是《`SQL`必知必会》书中的栗子。五个表分别是：

> - `Vendors`：存储销售产品的供应商信息，每个供应商对应一条记录
> - `Products`：产品目录，每行对应一个产品
> - `Customers`：存储顾客信息的表
> - `Orders`：存储顾客订单（订单日期，订单顾客ID）
> - `OrderItems`：订单的详细信息，每个订单中每个物品对应一行数据

<!--MORE-->

![](https://s2.ax1x.com/2019/10/04/uDsgOO.md.jpg)

### 排序

排序查询是通过`order by`关键字实现，位置一定是select语句的最后一个子句

- 单个字段直接排序
- 多个字段：按照指定的字段顺序进行排序，仅仅只有前面的字段有相同值，才会对后面的字段进行排序
- 按照列的相对位置进行排序

#### 直接按照字段排序

```sql
-- 单个排序字段
select prod_name 
from Products
order by prod_name;   -- 按照名字的字母进行排序，最后的语句

-- 多个排序字段
select prod_id, prod_price, prod_name
from Products
order by prod_price, prod_name;  -- 多个字段按照顺序查询
```

#### 列的位置排序

```sql
-- 相对位置排序（和上面的可以混合使用）
select prod_id, prod_price, prod_name
from Products
order by 2, 3;  -- 2:prod_price，3:prod_name
```

#### 指定排序方向

排序默认是升序`asc`，可以改成降序`desc`

```sql
select prod_id, prod_price, prod_name
from Products
order by prod_price desc, prod_name;   -- 先对prod_price降序，再对prod_name升序
```



### 过滤

过滤查询的关键字是`where`。`order by` 语句必须在`where`语句之后使用。两个特殊的操作符：

- `<>`和`!=`等价，都是不等于
- `!<`和`>=`等价

#### 查询范围值

```sql
select prod_name, prod_price 
from Porducts
where prod_price < 10;
where prod_price != 20;
where prod_price between 10 and 20;
```

#### 空值查询

通过`IS NULL`实现

```sql
select prod_name, prod_price 
from Porducts
where prod_price is null;
```

#### 多个条件

在查询的过程中可以同时使用`and`和`or`

- `and`优先级在前
- `()`优先级最高

```sql
select prod_name, prod_price 
from Porducts
where prod_price < 10 and vend_id = 'DLL01'    -- and
where vend_id = 'BRS01' or vend_id = 'DLL01'   -- or
where (vend_id = 'BRS01' or vend_id = 'DLL01')   -- 加上()保证先执行  
      and prod_price >= 10;        -- and优先级在前
```

#### `in`操作符

`in`取的是逗号分隔，括号里面的值，主要特点是

- 语法清晰、更加直观
- 比`or`操作符更快
- 在in操作符中可以包含其他的`select`语句

```sql
select prod_name, prod_price 
from Porducts
where vend_id in ('BRS01','DLL01')   --等价于or语句
order by prod_name;    
```

**一个not操作符号的栗子**

```sql
select prod_name
from Products
where not vend_id = 'DLL01'   -- 等价于vend_id <> 'DLL01'
order by prod_name;
```

