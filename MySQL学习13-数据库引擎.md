---
title: MySQL学习13-数据库引擎
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-12 09:04:33
password:
summary:
copyright: true
tags:
  - MySQL
  - 数据库引擎
categories:
  - MySQL
  - 进阶
---

### 什么是数据库引擎

> 数据库存储引擎是数据库底层软件组织，数据库管理系统`DBMS`使用数据引擎进行创建、查询、更新和删除数据。
>
> 不同的存储引擎提供不同的**存储机制、索引技巧、锁定水平**等功能，使用不同的存储引擎，还可以 获得特定的功能。不同的数据库管理系统都支持多种不同的数据引擎。
>
> `MySQL`数据库的核心就是存储引擎。在`MySQL`中不需要在整个服务器中使用同一个数据库引擎，针对具体的表可以使用不同的数据库引擎。

[MySQL数据库引擎](https://www.cnblogs.com/0201zcr/p/5296843.html)

<!--MORE-->

### 常用数据库引擎

- MyISAM：支持全文索引、不支持事务、支持表级锁、崩溃恢复性能不好；数据主要是用来插入和查询，首选是`MyISAM`。它是在`Web`、数据仓储和其他应用环境下最常使用的存储引擎之一
- InnoDB：`MySQL`默认引擎，不支持全文索引（5.6以后开始支持）、支持行级锁、速度快、性能好；要提供提交、回滚、崩溃恢复能力的事务安全（`ACID`兼容）能力，并要求实现**并发控制**，`InnoDB`最佳
- Memory：内存式引擎，临时存放数据，数据量不大，不需要较高的数据安全性；将数据存储到内存中。
- CSV：专门用来处理Excel 
- Archive：若只有`INSERT`和`SELECT`操作，可选择`Archive`，Archive**支持高并发**的插入操作，本身是非事务安全的。`Archive`非常适合存储归档数据，如记录日志信息可以使用`Archive`

![](https://s2.ax1x.com/2019/10/12/uLwfUS.md.png)

**一个数据库中多个表可以使用不同引擎以满足各种性能和实际需求**，使用合适的存储引擎，将会提高整个数据库的性能



### 常用操作

```sql
-- 查看数据库版本
mysql --version

-- 查看支持的引擎
show engines;

-- 查看数据库引擎
show variables like "storage_engine";

-- 创建表的时候指定数据库引擎
create table test (name varchar(5)) ENGINE=MyISAM;

-- 修改表的引擎
alter table test engine=InnoDB;
```

### MyISAM和InnoDB对比

#### InnoDB

- 支持`ACID`事务，实现了四种隔离级别
- 提供行级锁和外键约束，处理大容量数据系统
- 基于`MySQL`后台的完整数据库系统，`MySQL`运行时`Innodb`会在内存中建立缓冲池，用于缓冲数据和索引
- 由于锁的粒度更小，写操作不会锁定全表，所以在并发较高时，使用`Innodb`引擎会提升效率。
- 没有保存表的行数，使用`select`语句会扫描全表。
- 采用的是`B+tree`的索引结构，

#### MyISAM

- 不支持事务特性

- 不支持行级锁和外键约束

- 当进行插入`insert`和更新`update`操作会锁定整个表，效率低下

- 存储了表的行数，则当使用`select count(*) from table` 时，只需要直接读取已经保存好的值，而不需要进行全表扫描

- **读的操作远多于写**，且不支持事务时，`MyISAM`是首选

  





