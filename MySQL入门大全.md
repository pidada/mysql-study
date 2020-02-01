---
title: MySQL入门大全
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-02-01 11:02:20
password:
summary:
tags:
  - mysql
  - sql
  - 数据库
categories:
  - MySQL
  - 基础
---

### MySQL数据库入门大全

由于毕业后第一份工作的部分原因，必须好好地再学习下数据库，重点是`MySQL`。本文总结了自己学习过程中的全部历程，有数据库的基本概念介绍，有`MySQL`数据库中的`4`种语言，也有《SQL必知必会》一书的总结，更有`SqlZoo`网站由浅入深的配套练习

**本文既是自己的学习记录与总结，也可以作为数据库尤其是`MySQL`的入门资料**

<!--MORE-->

### 学习资料汇总

自己在学习的过程中使用了一些资料，**有网课，有书籍，也有网站**，包含：

- [网易课程-MYSQL从入门到精通](https://study.163.com/course/courseMain.htm?courseId=1005092013)
- **《SQL必知必会》**
- [SqlZoo练习](https://sqlzoo.net/)

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbg2u13zolj30c806wq3p.jpg)

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbg2s5za7ej30u0140dm8.jpg)

> ![image-20200131215222887](https://tva1.sinaimg.cn/large/006tNbRwgy1gbg33gk5cfj30o408mwfv.jpg)



### MySQL安装

`MySQL`现在主要是`5.X`和`8.0`；本文中介绍的是在`Ubuntu18.04`中如何安装[MySQL5.7](https://www.jianshu.com/p/430f61337a2e)

### MySQL数据库基本概念

数据库中的常见的[概念](https://www.jianshu.com/p/c71aa0199107)

- 数据库

- 表

- 列、字段

- 行、记录

- 主键

  

### MySQL数据类型、字段修饰语、运算符

`MySQL`数据库中的主要[数据类型](https://www.jianshu.com/p/af46e2a39fa0)

- 数值型
- 浮点型
- 字符串类型
- 日期时间类型

----

[字段修饰语和运算符](https://www.jianshu.com/p/10878072fe93)如下

**字段修饰语包含**

| 修饰语         | 作用                             |
| :------------- | :------------------------------- |
| unsigned       | 无符号                           |
| auto_increment | 自增                             |
| default        | 默认值                           |
| comment        | 字段解释说明                     |
| not null       | 非空                             |
| null           | 空                               |
| unique         | 唯一索引                         |
| index          | 普通索引                         |
| primary key    | 主键，必须指定为`auto_increment` |

**常见的运算符**

- = ：表示赋值或者判断
- !=或者<>：不等于
- <：小于
- \>：大于
- <=、>=：小于等于或者大于等于
- OR：或
- AND：与
- BETWEEN…AND…：在…和…之间
- IN/NOT IN ：在或不在

### MySQL中的4种语言

数据库中有常见的[4种语言]( https://www.jianshu.com/p/1e23f566364f)和[4种语言的知识点汇总](https://www.jianshu.com/p/3b6cab3b62f6)

1. [数据定义语言DDL](https://www.jianshu.com/p/c5610beb6137)
2. [数据操作语言DML](https://www.jianshu.com/p/f513350202b7)
3. [数据控制语言DCL](https://www.jianshu.com/p/df52c7346f7d)
4. [数据查询语言DQL](https://www.jianshu.com/p/083a6505ec83)



### MySQL数据库进阶

1. [数据库引擎](https://www.jianshu.com/p/5315d6b30eae)
2. [数据库字符集](https://www.jianshu.com/p/8706fa7ac392)
3. [int-char-varchar](https://www.jianshu.com/p/cbd358d391de)
4. [MySQL事务](https://www.jianshu.com/p/5bdc80bba1d7)
5. [MySQL存储过程](https://www.jianshu.com/p/364856a3eb5f)
6. [MySQL游标cursor](https://www.jianshu.com/p/42cbf8c27b89)

### SQL必知必会

1. [SQL必知必会1](https://www.jianshu.com/p/f5bf541eeefe)
2. [SQL必知必会2](https://www.jianshu.com/p/7ca1fe0c16a9)
3. [SQL必知必会3](https://www.jianshu.com/p/221060032944)

### sqlzoo练习

`sqlzoo`是一个专门用来练习`SQL`语句查询的网站，上面分成了各个板块或者语句。先是网站的例题，用户可以稍微修改提交，然后有各种由简到难的查询练习，很适合学习`SQL`的小伙伴进行入门。

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbg4e1firuj30l50h0abv.jpg)

#### select子句顺序

1. select
2. from
3. where
4. group by
5. having
6. order by（desc是降序）

#### 练习部分

1. [where的使用](https://www.jianshu.com/p/408dfe8d2b22)
2. [select的使用1](https://www.jianshu.com/p/5b0bd1b15db8)
3. [select的使用2](https://www.jianshu.com/p/dca016282919)
4. [Nobel Quiz-通配符与group by](https://www.jianshu.com/p/0d1cc3dc711b)
5. [select in select](https://www.jianshu.com/p/3f679a466a0c)
6. [select in select-quiz](https://www.jianshu.com/p/9770145e4e38)
7. [聚合函数](https://www.jianshu.com/p/fb0d91e15d94)
8. [sum-count-quiz](https://www.jianshu.com/p/0d01e6693e25)
9. [简单的join操作](https://www.jianshu.com/p/b07039f09a8d)
10. [join-quiz-1](https://www.jianshu.com/p/eee8271e17e6)
11. [more-join](https://www.jianshu.com/p/39577e371caa)
12. [join-quiz-2](https://www.jianshu.com/p/2a966e14826d)
13. [using-NULL](https://www.jianshu.com/p/8f6714966549)
14. [NULL-quiz](https://www.jianshu.com/p/93b1cc951571)
15. [self-join](https://www.jianshu.com/p/2c7401aeee16)
