---
title: 快速入门SQL
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-07-30 00:01:09
password:
summary:
tags:
  - 数据库
  - MySQL
categories:
  - MySQL
  - 基础
---

### 快速入门SQL

快速复习一下之前学的SQL。虽然`SQL`对大小写不敏感，**建议关键字使用大写**，但是我习惯了小写

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh4i0nx5qtj31xx0u0x6w.jpg)



<!--MORE-->

### insert

`insert into` 语句用于向表格中插入新的行

```sql
INSERT INTO 表名称 VALUES (值1, 值2,....) -- 可以省略id值
```

我们也可以指定所要插入数据的列：

```sql
INSERT INTO table_name (列1, 列2,...) VALUES (值1, 值2,....)  -- 每个字段和值要一一对应
```

### udpate

用于修改表中的数据

```sql
update tablename set col=value where 条件;

update information set age=18 where name='xiaoming';   -- 将xiaoming的年龄设置成18

update information set age=18 where name in ('xiaoming','xiaohong','yangqiang');
```



### delete

DELETE 语句用于删除表中的行

```
DELETE FROM 表名称 WHERE 列名称 = 值;
```

删除某行

```sql
delete from tablename where name='xiaoming';
```

删除全部行

```sql
delete * from tablename;
delete from tablename;
```



### select

```sql
select 列名 from  表名

select * from tableName
select col1,col2,...coln from tableName
```



### 去重distinct

```sql
select distinct 列名 from 表名
```



### 定位where

```sql
SELECT 列名 FROM 表名 WHERE 具体条件
```

| 操作符  | 描述         |
| :------ | :----------- |
| =       | 等于         |
| <>      | 不等于       |
| >       | 大于         |
| <       | 小于         |
| >=      | 大于等于     |
| <=      | 小于等于     |
| BETWEEN | 在某个范围内 |
| LIKE    | 搜索某种模式 |

其中`<>`等同于`!=`

**attention：具体条件值周围使用的是单引号**

`SQL`使用单引号来环绕*文本值*（大部分数据库系统也接受双引号）。如果是*数值*，请不要使用引号。

![image-20200729205753873](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh852bcwvdj319g0saadk.jpg)

### AND-OR

`AND` 和`OR` 可在 `WHERE` 子语句中把两个或多个条件结合起来。

如果第一个条件和第二个条件都成立，则 AND 运算符显示一条记录。

如果第一个条件和第二个条件中只要有一个成立，则 OR 运算符显示一条记录。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh85f4mc3qj319g0ewwg9.jpg)

### ORDER BY 语句

ORDER BY 语句用于根据指定的列对结果集进行排序，默认是升序，关键字desc，可以是字母顺序或者数字大小顺序

```sql
SELECT Company, OrderNumber,Name,Age FROM Information ORDER BY Age DESC   -- 单个排序

SELECT Company, OrderNumber FROM Orders ORDER BY Company DESC, OrderNumber ASC -- 多个排序
```

### top

TOP 子句用于规定要返回的记录的数目，通过limit来实现

```sql
select col from tablename limit 1000; -- 返回1000条
```



### like

用于在 WHERE 子句中搜索列中的指定模式

- 左匹配
- 右匹配
- 左右匹配

```sql
select name from information
where name like '%ming'

select name from information
where name not like 'xiao%'   -- 和not连用

select name from information where like '%aomi%'
```

### 通配符

在搜索数据库中的数据时，`SQL` 通配符可以替代一个或多个字符。`SQL`通配符必须与`LIKE`运算符一起使用。

在`SQL` 中，可使用以下通配符：

| 通配符                     | 描述                   |
| :------------------------- | :--------------------- |
| %                          | 替代一个或多个字符     |
| _                          | 仅替代一个字符         |
| [charlist]                 | 字符列中的任何单一字符 |
| [^charlist]或者[!charlist] | 不在字符列中的任何单一 |

### between

用在where子句中，作用是取介于两个值之间的数据范围

```sql
select name, age from information
where age [not] between 18 and 25;  -- 可以和not连用
```

### 别名使用alias

通过使用 SQL，可以为列名称和表名称指定别名（Alias）

```sql
select age from personInformation as per;   -- 表的别名是per

select column_age as Age from information;  -- 列的别名是Age
```

### join（inner join）

有时为了得到完整的结果，我们需要从两个或更多的表中获取结果。我们就需要执行 join操作。

数据库中的表可通过键将彼此联系起来。主键（Primary Key）是一个列，在这个列中的每一行的值都是唯一的。在表中，每个主键的值都是唯一的。这样做的目的是在不重复每个表中的所有数据的情况下，把表间的数据交叉捆绑在一起。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh8ajoxiuvj314s0u079i.jpg)

通过id_p将两个表连接起来了

**如何查询谁订购了什么产品呢**？

```sql
-- 不使用join，用where
select Persons.LastName, Persons.FirstName, Orders.OrderNo
from Persons, Orders
where Persons.Id_P = Orders.Id_P;  -- 加入限制条件
```

```sql
-- 使用join

select Persons.LastName, Persons.FirstName, Orders.OrderNo
from Persons   -- 左边的表
INNER join Orders  -- 右边的表
on Persons.Id_P = Orders.Id_P   -- 共同字段
order by Persons.LastName  -- 排序
```

4种不同类型join的区别：

- JOIN或者inner join: 如果表中有至少一个匹配，则返回行
- LEFT JOIN: 即使右表中没有匹配，也从左表返回所有的行
- RIGHT JOIN: 即使左表中没有匹配，也从右表返回所有的行
- FULL JOIN: 只要其中一个表中存在匹配，就返回行

### left join

如下图中的两张表，**列出所有的人**，以及他们的定购 - 如果有的话

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh8atlxazlj31a40ta0vk.jpg)

```sql
select Persons.LastName, Persons.FirstName, Orders.OrderNo
from Persons
left join Orders
on Persons.Id_P=Orders.Id_P
order by Persons.LastName;
```

| LastName | FirstName | OrderNo |
| :------- | :-------- | :------ |
| Adams    | John      | 22456   |
| Adams    | John      | 24562   |
| Carter   | Thomas    | 77895   |
| Carter   | Thomas    | 44678   |
| Bush     | George    |         |

**最后的一行出现了空格**

### right join

RIGHT JOIN 关键字会右表 (table_name2) 那里返回所有的行，即使在左表 (table_name1) 中没有匹配的行

```sql
SELECT column_name(s)
FROM table_name1
RIGHT JOIN table_name2
ON table_name1.column_name=table_name2.column_name
```

还是上面的表：**列出所有的订单**，以及订购它们的人-如果有的话

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh8atlxazlj31a40ta0vk.jpg)

```sql
select Persons.LastName, Persons.FirstName, Orders.OrderNo
from Persons
right join Orders
on Persons.Id_P=Orders.Id_P
order by Persons.LastName;
```

| LastName | FirstName | OrderNo |
| :------- | :-------- | :------ |
| Adams    | John      | 22456   |
| Adams    | John      | 24562   |
| Carter   | Thomas    | 77895   |
| Carter   | Thomas    | 44678   |
|          |           | 34764   |

**左边的表中出现了空行**

### Full join

只要其中某个表存在匹配，FULL JOIN 关键字就会返回行。

还是上面的表格，列出所有的人以及他们的订单以，及所有的订单和订购它们的人

```sql
SELECT Persons.LastName, Persons.FirstName, Orders.OrderNo
FROM Persons
FULL JOIN Orders
ON Persons.Id_P=Orders.Id_P
ORDER BY Persons.LastName
```

| LastName | FirstName | OrderNo |
| :------- | :-------- | :------ |
| Adams    | John      | 22456   |
| Adams    | John      | 24562   |
| Carter   | Thomas    | 77895   |
| Carter   | Thomas    | 44678   |
| Bush     | George    |         |
|          |           | 34764   |

上面出现了两个空格
