---
title: MySQL学习5_DML
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-02 09:05:37
password:
summary:
copyright: true
tags:
  - 数据库
  - MySQL
  - 数据操作语言
categories:
  - MySQL
  - 基础
---

### DML

> DML，`data manipulation language`，数据操作语言。主要是对数据库中的表记录进行操作的语言，包含往表中插入数据、表中数据的更新、表的删除等

- 表中插入数据

- 表中数据更新

- 删除表

  <!--MORE-->

### 复习创建表

在对表中的数据进行操作之前复习`DDL`（数据定义语言）中关于如何在数据库中创建一个表`user`：

```sql
create table user(
    id int(10) unsigned not null auto_increment comment "user_id",
    name varchar(20) not null comment "用户名",
    age tinyint unsigned not null comment "用户年纪",
    email varchar(50) not null comment "用户邮箱",
    fee decimal(10,2) not null default 0.00 comment "用户余额",
    create_time timestamp not null comment "注册时间",
    primary key(id)
);
```

### 表中插入数据

```sql
-- 将字段名和字段的值一一对应起来，可以只插入部分字段
-- 省略了id和createtime字段
mysql> insert into user(
            name, 
            email, 
            age, 
            fee, 
            password) 
values("xiaoming", 
           "123456@qq.com", 
           20, 
           56.56, 
           Password("xiaoming")     // 密码这里要用函数Password()
      );
```

```sql
  -- 包含所有字段信息
  insert into user values(10, "nanan", "78173828@qq.com", 38, 89.19, 2019-10-02, Password("nanan"));
```

- 字符串字段必须用引号括起来
- 密码需要使用函数`Password()`
- 语句末尾加分号
- 利用只插入部分字段
- 可以省去字段名，此时需要加上`id`，而且必须填写所有的字段信息，不能只添加部分数据

![](https://s2.ax1x.com/2019/10/02/uaYh2q.png)

### 数据更新

数据更新`update`使用最多的是`where`语句，指定某个条件下执行；如果不加`where`，则所有的字段都会被更改（慎重）

- 指定`id`号
- 指定字段的具体值
- 字段允许有多个，用逗号隔开

```sql
mysql> update user set name="nangying" where id=6;   // 通过id指定
mysql> update user set fee=88.76 where fee=56.90;   // 通过字段名直接指定
mysql> update user set email="81847919@qq.com", age=54 where id=7;  // 同时修改多个值
mysql> update user set fee=88.88 where id in(2,4,6);   // in的用法
mysql> update user set fee=66.66 where id between 2 and 6;  // between ... and ...
```

### 删除

- 删除表有两种情况：
  - `delete`：删除表，插入数据`从上一次结束`的`id`号开始继续插入；删除的记录仍存在
  - `truncate`：清空表，重新插入数据`id`从1开始；不占内存空间

```sql
delete table user;     
truncate table user;
```

- 删除`delete`表中的某条记录

```sql
delete from user where id=7;   // 删除记录
insert into user (name,email,age,fee,password) values("lisi","9837194@qq.com", 36, 81.17, Password("lisi"));  // id是从原来的基础上递增
```



![](https://s2.ax1x.com/2019/10/02/uaUVDf.md.png)

![](https://s2.ax1x.com/2019/10/02/uadxbV.png)

#### 关于truncate

```sql
# 删除数据
mysql> truncate table user;
Query OK, 0 rows affected (0.05 sec)

mysql> select * from user;
Empty set (0.00 sec)

# 重新插入数据
mysql> insert into user (user_name, user_email, user_age, password, fee) values ("peter", "123456a@163.com", 27, password("101010"), 28.87);
Query OK, 1 row affected, 2 warnings (0.01 sec)

mysql> select * from user;
+----+-----------+-----------------+----------+--------------------------------+-------+
| id | user_name | user_email      | user_age | password                       | fee   |
+----+-----------+-----------------+----------+--------------------------------+-------+
|  1 | peter     | 123456a@163.com |       27 | *C3BC3E91915DCAE22014892F9827D | 28.87 |
+----+-----------+-----------------+----------+--------------------------------+-------+
1 row in set (0.00 sec)
```

