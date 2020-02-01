---
title: MySQL学习1_基本概念
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-01 12:32:43
password:
summary:
copyright: true
tags:
  - mysql
  - 数据库
categories:
  - MySQL
  - 基础
---

> 国庆期间准备花个两天时间将之前学习的`MySQL`的基础知识再次复习下，顺便将笔记整理的更好些，从`JS`全部迁移到站点上。知识点主要是数据库基本的`增删改查`、事务和游标等基础性知识，数据库入门墙裂推荐`网易云`的如下课程：

[MySQL从入门到精通](https://study.163.com/course/courseMain.htm?courseId=1005092013&_trace_c_p_k2_=2b1405f210324cc3acfa4501b6151f6f)

另外，自己配套看了《`SQL`必知必会》一书，写的很具体，不愧是经典书籍

<!-- MORE -->

![](https://s2.ax1x.com/2019/10/01/uNR7VA.jpg)

----

### 概念

- 数据库`database`：以某种有组织的方式存储的数据集合。通常是一个文件或者一组文件。

- 表`table`：结构化的文件，用来存储某种特定类型的数据。存储在表中的数据是同一种类型的数据或者清单。表名具有唯一性，表的特性：

  - 数据在表中如何存储
  - 存储什么类型的数据
  - 数据如何分解
  - 各个部分的信息如何命名

- 列`column`：表由列组成，列存储表中的某部分的信息。**列是表中的字段**。表中每个列存储特定的信息，比如商品的表中有：商品的名称、生产日期、产地等。每个列都有相应的数据类型

- 行`row`：表中的数据是按照行存储的，所保存的记录是存储在自己的行内。比如顾客表中，每行存储一个顾客的相关信息。有时候行业称之为记录`record`

  **垂直的列为表列，水平行为表行**

- 主键`primary key`：一列（或一组列），能够唯一标识表中每一行，比如订单表中的订单`ID`。主键是用来表示一个特定的行。表中任何一列都是可以作为主键，满足条件：

  - 任意两行都不具有相同的主键值
  - 每一行都具有相同的主键值
  - 主键的值不允许修改或者更新
  - 主键值不能重用

![](https://s2.ax1x.com/2019/10/12/uXtfZ8.md.png)

### `SQL`语句分类

结构化查询语言`Structured Qurey Language`，语句必须是以分号结束，且关键词不区分大小写，主要包含四种语句

- `DDL，data defination language`数据定义语言
- `DML，data manipulation language`数据操作语言
- `DCL，data control language`数据控制语言
- `DQL，data query language`数据查询语言

#### DDL

- `data defination language`，数据定义语言；作用主要是：`创建、删除、修改库表结构`。
- 针对库表结构

#### DML

- `data manipulation language`，数据操作语言；作用主要是：增、删、改表的记录
- 针对`表`的操作

#### DCL

- `data control language`，数据控制语言；用户创建及授权操作
- 针对数据库的`用户`

#### DQL

- `data query language`，数据查询语言；查询数据
- 主要是`查询`功能

**关于注释问题**

```sql
# 注释

-- 注释

/*
注释内容1
注释内容2
*/
```

