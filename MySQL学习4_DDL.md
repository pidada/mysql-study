---
title: MySQL学习4_DDL
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-01 16:00:56
password:
summary:
copyright: true
tags:
  - 数据库
  - MySQL
  - 数据定义语言
categories:
  - MySQL
  - 基础
---

### 关于进入数据库

```bash
mysql -uroot -p    //使用这种方式，接下来需要输入密码。密码是暗文
mysql -uroot -p123456  // 可以直接将密码123456放在参数p的后面，不安全
```

参数解释：

- u：指定用户

- p：指定密码

  ![](https://s2.ax1x.com/2019/10/01/uUAhAe.md.png)

  ----

### DDL

`DDL，data defination language`，指的是数据定义语言，其主要作用是创建数据库，对库表的结构进行删除和修改等操作。



### 全部命令

```sql
1. 数据库操作
show databases;     // 显示所有的数据库
use school;     // 使用school数据库
create database school;  // 创建数据库
drop database school;  // 删除某个数据库

2. 表操作
-- 创建表
create table user(字段1,字段2,...,字段n);   

-- 查看创建表的SQL语句
show create table user;

-- 查看表的结构
desc user;

-- 删除表
drop table user;

-- 修改表名
alter table user rename to users;   
```



<!--MORE-->

### 数据库操作

```sql
show databases;     // 显示所有的数据库
use school;     // 使用school数据库
create database school;  // 创建数据库
drop database school;  // 删除某个数据库
```

```sql
mysql> show databases;   // 显示数据库
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| peter              |
| school             |
| sys                |
+--------------------+
6 rows in set (0.04 sec)

mysql> use school;   // 选择使用数据库
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
```

### 表操作

```
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

- 最后的分号不能忘记:cry:
- 最后的分号不能忘记:cry:
- 最后的分号不能忘记:cry:

```sql
mysql> use school;  // 使用一个数据库
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;  //  查看数据库下的所有表
+------------------+
| Tables_in_school |
+------------------+
| course           |
| score            |
| student          |
| teacher          |
| total            |
+------------------+
5 rows in set (0.00 sec)
```

#### 创建表

- 主键`primary key` 和`auto_increment`必须连在一起使用
- 书写规范：每个字段的语句最好分行写，容易检查
- 最后的分号不能忘记

```sql
# 创建user表：6种字段+1个主键

create table user( id int(10) unsigned not null auto_increment comment "user_id",   //将id作为主键
                  name varchar(20) not null comment "user_name", 
                  email varchar(50) not null comment "user_email", 
                  age tinyint unsigned not null comment "user_age", 
                  fee decimal(10,2) not null default 0.00 comment "user_fee", 
                  createTime timestamp not null comment "user_time", 
                  primary key(id)  
                 );    // 记得分号
```

![](https://s2.ax1x.com/2019/10/01/uUZZlR.png)

#### 查看表结构

```sql
mysql> desc user;
+------------+---------------------+------+-----+-------------------+-----------------------------+
| Field      | Type                | Null | Key | Default           | Extra                       |
+------------+---------------------+------+-----+-------------------+-----------------------------+
| id         | int(10) unsigned    | NO   | PRI | NULL              | auto_increment              |
| name       | varchar(20)         | NO   |     | NULL              |                             |
| email      | varchar(50)         | NO   |     | NULL              |                             |
| age        | tinyint(3) unsigned | NO   |     | NULL              |                             |
| fee        | decimal(10,2)       | NO   |     | 0.00              |                             |
| createTime | timestamp           | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
+------------+---------------------+------+-----+-------------------+-----------------------------+
6 rows in set (0.02 sec)
```

#### 查看创建表的`SQL`语句

```sql
show create table user;
```

![](https://s2.ax1x.com/2019/10/01/uUZUnP.png)

#### 字段操作

关键词是`alter`，先选中需要操作的表。

- `modify`：修改
- `change`：改变名字
- `add`：添加字段
  - 默认是末尾
  - 指定位置添加

```sql
// 修改字段信息
alter table user modify name varchar(50) not null;   # 将字段name 从20改为50个字符

// 修改字段名字
alter table user change email user_email varchar(50) not null;  # 将email改成user_email

// 末尾添加字段
alter table user add password char(30) not null comment "user_password";   # 增加password字段

// 指定位置添加字段
alter table user add password1 char(30) not null comment "user_password1" after user_name;   # 在name后面增加password1字段

// 删除字段
alter table user drop password1;        #删除字段password1
```

```sql
// 原来的表格信息

mysql> desc user;
+------------+---------------------+------+-----+-------------------+-----------------------------+
| Field      | Type                | Null | Key | Default           | Extra                       |
+------------+---------------------+------+-----+-------------------+-----------------------------+
| id         | int(10) unsigned    | NO   | PRI | NULL              | auto_increment              |
| name       | varchar(20)         | NO   |     | NULL              |                             |
| email      | varchar(50)         | NO   |     | NULL              |                             |
| age        | tinyint(3) unsigned | NO   |     | NULL              |                             |
| fee        | decimal(10,2)       | NO   |     | 0.00              |                             |
| createTime | timestamp           | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
+------------+---------------------+------+-----+-------------------+-----------------------------+
6 rows in set (0.00 sec)

# 修改字段信息
mysql> alter table user modify name varchar(50); 
# 修改字段名字
mysql> alter table user change email user_email varchar(50) not null;
# 添加字段，末尾
mysql> alter table user add password char(30) not null comment "user_password";
# 指定位置添加字段
mysql> alter table user add password1 char(30) not null comment "user_password1" after name;

mysql> desc user;
+------------+---------------------+------+-----+-------------------+-----------------------------+
| Field      | Type                | Null | Key | Default           | Extra                       |
+------------+---------------------+------+-----+-------------------+-----------------------------+
| id         | int(10) unsigned    | NO   | PRI | NULL              | auto_increment              |
| name       | varchar(50)         | YES  |     | NULL              |                             |
| password1  | char(30)            | NO   |     | NULL              |                             |
| user_email | varchar(50)         | NO   |     | NULL              |                             |
| age        | tinyint(3) unsigned | NO   |     | NULL              |                             |
| fee        | decimal(10,2)       | NO   |     | 0.00              |                             |
| createTime | timestamp           | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
| password   | char(30)            | NO   |     | NULL              |                             |
+------------+---------------------+------+-----+-------------------+-----------------------------+
8 rows in set (0.00 sec)
```

