---
title: SQL进阶-2-自连接
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-08-30 13:49:25
password:
summary:
tags:
  - MySQL
  - 数据库
categories:
  - MySQL
  - 进阶
---



### SQL进阶-2-自连接

`SQL`通常在不同的表之间进行连接运算，但是也可以在相同的表中进行运算。`macOS`中连接数据库：

```mysql
 mysql -u root -p   -- 后续输入密码
```





![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4nadc6dlj30bw0h4tcv.jpg)

<!--MORE-->

### 基本操作

#### 创建和查看数据库

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8mjyy8psj30gs0igjso.jpg)



![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8mn5ontjj30tq0xcaed.jpg)

```mysql
// 1、创建表
create table user(字段1,
                  字段2,
                  ...,
                  字段n
                  );    // 最后的分号不能忘记

// 2、查看所有的表
show tables;

// 3、查看表的结构
desc user;

// 4、查看创建表的SQL语句
show create table user;

// 5、删除表
drop table user;

// 6、修改表名
alter table user rename to users;   # 表名改为users；to可省略

```

```mysql
mysql> create table products (id int primary key auto_increment,name varchar(50) default null, price int default null);
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8n0k72k9j30vg0u0q8j.jpg)



#### 问题

今天在往数据库中的表插入`中文数据`的时候遇到的一个问题：**绝对是编码的问题**

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8nebboctj31t809y43z.jpg)



解决过程

1. 修改数据库编码



```mysql
mysql> alter table products  default character set utf8;   -- 直接修改数据库的编码，设置成utf8
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8rzu9qd1j315e0putc9.jpg)



**虽然修改了整个表的编码，但是name字段的编码还是latin1，不能插入中文，需要修改**

之前的编码：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8nqammeoj30ve0faabw.jpg)



2. 修改具体字段name的编码

关键词是modify

```mysql
mysql> alter table products modify column name varchar(50) character set utf8;
```

语法格式为：

```mysql
mysql>alter table <表名> modify column <字段> <字段类型> character set utf8;   --将字段改成utf8类型
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8s7zi9luj327k07wgnv.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8s8g0uvcj30oa0keq4k.jpg)

#### 插入新数据

```mysql
insert into <表名> <字段1，字段2，.....> values <对应的数据>
```



![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8sfy06shj31kb0u0dkh.jpg)

### 案例1-可重排、组合

#### 2种组合

- 有顺序的有序对，用尖括号括起来
- 无顺序的无序对，使用花括号括起来

#### 需求

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi5u7jum57j30gh073401.jpg)

SQL自连接生成的结果中相同的数据

```mysql
select p1.name as name1, p2.name as name2
from products p1, products p2
```

```mysql
name_1       name_2￼
------      ------￼
苹果         苹果￼    -- 出现相同的结果，如何去掉
苹果         橘子￼
苹果         香蕉￼
橘子         苹果￼
橘子         橘子￼
橘子         香蕉￼
香蕉         苹果￼
香蕉         橘子￼
香蕉         香蕉
```

```mysql
select p1.name as name1, p2.name as name2
from products p1, products p2
where p1.name <> p2.name   -- 指定两个表的中的2个名字不等
```

> 笔记：在SQL中即便是相同的表，只要是被赋予了不同的名称，都应该被当做不同的表来使用，它们只是碰巧存储了相同数据的两个集合
>
> 表的自连接和不同表间的连接没有任何区别

#### 扩展

想要获取3个以上的元素，通过多个表的连续扩展：

```mysql
select P1.name as name1, P2.name as name2, P3.name as name3
from Products P1,Products P2, Products P3
where P1.name > P2.name   -- 只与字符顺序比自己靠前的商品进行比较
and P2.name2 > P3.name
```



### 案例2-重复行问题

#### 查找重复行

##### 基于单个字段

假设我们想通过name字段（只根据一个字段）来查找重复行，可以使用如下语句：

```mysql
select name, price, count(*)   -- 分组之后统计每个组的行数
from Products
group by name;
```

过滤小于1的行：

```mysql
select name, price,  count(*)
from Products
group by name
having count(*) > 1;  -- 过滤
```

> 笔记：根据具有相同值的字段分组，然后只显示大小大于1的组


##### 基于多个字段

有时候会基于多个字段查找重复行

```mysql
SELECT
    col1, COUNT(col1),
    col2, COUNT(col2),
    ...
FROM
    table_name
GROUP BY
    col1,
    col2,
    ...
HAVING  -- 只有当列的组合重复时，才认为是重复行，用and进行关联
       (COUNT(col1) > 1) AND
       (COUNT(col2) > 1) AND
       ...
```



#### 删除重复行

通过删除重复行变成如下的结果

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8k7kuswhj30nd0kngq5.jpg)



##### Oracle中使用极值函数rowid

```mysql
delete from Products P1
where rowid < (  -- 小于最大值的全部删除
  select max(P2.rowid)   -- 返回的是价格和名字都相同的商品所在的行
  from Products P2
  where P1.name = P2.name
  and P1.price = P2.price
)
```

- 子查询中返回的是价格和名字都相同的商品所在的行
- 苹果和香蕉没有重复行
- 橘子中有，返回的是最大值4，小于4的两行（2、3）都会被删除delete



##### 使用非等值连接

```mysql
delete from Products P1
where exists (  -- 存在于小于最大值的行全部删除delete
  select * from Products P2
  where P1.name = P2.name
  and P1.price = P2.price
  and P1.rowid < P2.rowid
)
```

#### mysql中实现删除重复行

[参考教程](https://www.yiibai.com/mysql/delete-duplicate-rows.html)：使用的是delete join语句，**保留最大的行记录，小于部分全部删除了**

```mysql
delete P1 from Products P1
inner join Products P2
where P1.id < P2.id   -- 假设数据中存在自增id
and P1.name = P2.name
```

### 案例3-局部不一致的列

#### 需求

在同一张表中，可能存在不是很合理的地方：比如，**前田夫妇的地址应该是相同的**，但是表中 不同

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8ll6i8zhj30vs0c1tdo.jpg)

#### SQL实现

1. 查找是同一家人，但是住址不同的记录

**自连接和非等值连接的结合**

```mysql
select distinct A1.name, A1.address
from Address A1, Address A2
where A1.family_id = A2.family_id  -- 同一家人
and A1.address <> A2.address;  -- 住址不同
```

2. 查找价格相等但是名称不同的商品的记录

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8m3mzlahj30h30dl40r.jpg)

```mysql
select distinct P1.name, P1.price
from Products P1, Products P2  -- 同一个表的自连接
where P1.price=P2.price   -- 价格相同但是名称不同
and P1.name <> P2.name  -- 非等值连接
```
![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8u7u12u1j30u01080xg.jpg)

**如果查询语句中没有distinct，结果中会出现重复值**

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8uv1wbahj310n0u0wkz.jpg)

### 非等值连接

使用`<、>、<>`等进行连接的称之为“非等值连接”。其中，>或者<等比较运算符不仅可以用于数值比较**，还可以用于比较字符串或者日期等的比较**


