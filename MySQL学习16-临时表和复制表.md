---
title: MySQL学习16-临时表和复制表
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-15 14:06:12
password:
summary:
copyright: true
tags:
  - MySQL
  - 表处理
categories:
  - MySQL
  - 进阶
---

### MySQL临时表

> `MySQL` 临时表在我们需要保存一些临时数据时是非常有用的。临时表只在当前连接可见，当关闭连接时，`Mysql`会自动删除表并释放所有空间。 

<!--MORE-->

#### 创建临时表

关键字是`temporary`

```sql
-- 创建临时表
create temporary table user(
    id int (10) unsigned not null auto_increment comment "用户id",
    name varchar(30) not null comment "用户名",
    email varchar(30) not null comment "邮箱",
    age tinyint unsigned not null commment "年龄",
    password varchar(30) not null comment "密码",
    primary key(id)     -- 创建自增主键
);

insert into user 
(name, email, age, password) 
values 
('xiaoming', 123456@qq.com, 25, Password('123456');
```

当退出了当前的`MySQL`对话，再次使用`select`命令来读取临时表中的数据，发现表已经被销毁了。

#### 删除临时表

关键字是drop

```sql
drop table user;
```



### 复制表



#### 只复制表结构到新表

```sql
create table new_table select * from old_table where 1=2;  -- 不会复制时的主键类型和自增方式

create table new_tabel like old_table;  -- 所有的字段类型复制到新表
```

#### 复制表结构和数据到新表

```sql
create table new_table select * from old_table;
```

#### 复制数据到新表

```sql
insert into new_tabel select * from old_table;    -- 两个表结构相同

insert into new_table(column1,.....) select column1, column2, ... from old_table;    -- 表结构不同
```

