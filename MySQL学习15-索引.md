---
title: MySQL学习15-索引index
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-14 20:24:42
password:
summary:
copyright: true
tags:
  - MySQL
  - index
categories:
  - MySQL
  - 进阶
---

> MySQL索引的建立对MySQL的高效运行是很重要的。索引可以大大提高MySQL的查询速度。
>
> 比如：新华字典的目录页（索引）快速查找汉字；图书馆通过首字母可以快速定位相关书籍，加快查找速度。

<!--MORE-->

### 索引分类

- **普通索引**：key(字段1,字段2,.....)

- **唯一索引**：unique key(字段1,字段2,.....)

- **主键索引**：primary key(字段1)   只能有一个
- **全文索引**：fulltext(字段1)
- **组合索引**：index(字段1,字段2,.....)
- **外键约束**

-----

### 索引功能

- 索引是应用在`MySQL`查询语句的条件（`where`语句的条件）

- 索引也是一张表，保存了主键和索引字段，并指向实体表的记录

### 索引缺点

- 虽然提高了查询速度（select），但是降低了更新表的速度（insert、update、delete）
- `MySQL`不仅要保存数据，还要保存索引文件；建立索引会占用磁盘空间的索引文件

----

### 普通索引

几种不同的创建方式

```sql
-- 创建表的时候直接指定
create table user(
    id int unsigned not null auto_increment,   -- auto_increment 和 primary key()必须连用，加快查询速度
    user_name varchar(20) not null,
    user_age tinyint(20) not null,
    user_email varchar(20) not null,
    password varchar(30) not null,
    primary key(id),    -- 主键    
    unique(user_name),  -- 唯一
    key(user_age),  -- 普通索引
    index MyIndex (user_email(length))  -- 普通索引
); 

-- 基本创建
create index myindex on table_name(username(length));

-- 修改表结构的时候添加索引
alter table tablename add index indexname(colunmName);
```

删除索引

``` sql
drop index [indexname] on table_name;
```

### 唯一索引

索引列的值必须唯一，但是允许有空值；如果是组合索引，则列值的组合必须唯一

####  创建索引

```sql
-- 创建索引
create unique index indexname on tablename(username(length))

-- 修改表结构的时候创建索引
alter table tablename add unique indexname (user(length))

-- 创建表的时候指定唯一索引
create table tablename(
    id int not null,
    username varchar(20) not null,
    unique indexname (username(length))
);

-- 实例创建：添加索引
alter table tablename add index (c);
```

####  删除索引

```sql
alter table tablename drop index c' 
```



#### 使用alter来创建索引

```sql
alter table tablename add primary key (column_list);  -- 添加主键
alter table tablename add unique index_name(column_list);   -- 添加唯一索引
alter table tablename add index indexname(column_list);  -- 普通索引
alter table tablename add fulltext indexname(column_list);  -- 添加全文索引
```

#### **关于fulltext**

>解决模糊查询问题，只支持英文；默认查询的内容有空格

- MyISAM支持全文索引
- InnoDB在MySQL5.6后开始支持全文索引

### 显示索引信息

 使用 `SHOW INDEX `命令来列出表中的相关的索引信息。可以通过添加` \G `来格式化输出信息 

```sql
show index from table_name; \G  -- 格式化输出信息
```

