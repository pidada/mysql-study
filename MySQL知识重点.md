---
title: MySQL知识重点
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-15 18:59:26
password:
summary:
copyright: true
tags:
  - MySQL
  - 面试
categories:
  - MySQL
  - 进阶
---

> MySQL知识点进行了一个小结

![](https://s2.ax1x.com/2019/10/15/KPPxF1.png)

### 引擎

#### MyISAM

- 不支持事务
- 支持表级锁
- 不支持MVCC
- 支持全文索引
- 不支持外键
- 堆表结构

<!--MORE-->

#### InnoDB

- 支持事务
- 支持行级锁
- 支持MVCC
- 支持外键
- 不支持全文索引
- 索引组织表结构

#### Memory

- 内存式引擎
- 临时存放数据，数据量小
- 安全性低

#### Archive

- 事务是非安全的
- 支持高并发
- 适合存储归档数据
- 对于select和insert语句，比较适合

### 事务

#### ACID

- 原子性Atomicity
- 一致性Consistency
- 隔离性 Isolation

  - 未提交读(Read Uncommitted)
  - 提交读(Read Committed)
  - 可重复读(Repeated Read)，默认
  - 串行读(Serializable)

- 持久性Durability

#### 术语

- start transaction
- end transaction
- commit transaction
- rollback transaction

### 索引index

#### 分类

- index

  - 普通索引，可以重复

- unique

  - 唯一索引，索引列的值必须唯一，可以为空

- 组合索引

  - 多个字段上创建的索引

- primary key

  - 主键索引，一个表只能有一个，且不能为空

- 全文索引fulltext

  - MyISAM 支持
  - InnoDB5.6以下不支持

#### 特点

- 提高查询速度
- 创建和维护耗费资源和时间
- 影响插入的速度

#### 效果

- show status like '%handler_read%'越大越好

### 语句

#### DDL

- 数据定义语言（create drop）

#### DML

- 数据操作语句（insert update delete）

#### DQL

- 数据查询语句（select ）

#### DCL

- 数据控制语句，进行授权和权限回收（grant revoke）

#### TPL

- 数据事务语句（commit collback savapoint）

### 语句优化

- 避免select *，将字段列出来
- 使用连接（join）来代替子查询
- 使用limit对查询结果的记录进行限定
- 用 exists 代替 in 
- where
  - 用Where子句替换HAVING 子句
  - 不要在 where 子句中的“=”左边进行函数等
  - 避免在where 子句中对字段进行 null 值判断
  - 避免在 where 子句中使用!=或<>操作符

---------

### 三范式

#### 第一范式

- 对属性的原子性约束
- 要求字段具有原子性，不可再分解

#### 第二范式

- 满足范式1
- 非主键字段不能出现部分依赖主键

#### 第三范式

- 满足范式 2
- 不能出现传递依赖

### 表结构优化

- 建表的时候设置主键
- 选择正确的存储引擎
- 使用简单的数据类型，整型比字符处理开销更小
- 使用合理的字段属性长度，固定长度的表会更快
- 使用enum、char而不是varchar
- 给频繁使用和查询的字段建立合适的索引
- 尽可能使用not null定义字段

### varchar和char

#### char

- 声明字符长度，0-255
- 当char值被存储时，会用空格填充到指定的长度
- char(n)：每个值占据n个字节

#### varchar

- 变长，多余的截掉
- 字符长度 0-65535
- varchar(n)：每个值占据n+1个字节，1个字节用来记录长度

