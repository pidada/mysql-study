---
title: MySQL50-1-创建表和数据
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-10-29 13:03:49
password:
summary:
tags:
  - MySQL
  - 数据库
categories:
  - MySQL
  - 50题
---



### MySQL经典50题-1-创建数据表和插入数据

本文的整理和学习来自CSDN的一位[博主](https://blog.csdn.net/fashion2014)，接下来的一个系列将是自己的学习和整理内容，提升MySQL。

同时解法会对网上的版本进行整理和综合，尽可能有多种答案。本文是复习表的创建语句

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gkgmq9quckj31d20gu0wv.jpg)
<!--MORE-->

### 表名和字段

涉及到的4个表和相关字段

#### 学生表

Student(s_id,s_name,s_birth,s_sex) ：学生编号、姓名、年月,、性别

#### 课程表

Course(c_id,c_name,t_id) ：课程编号、 课程名称、 教师编号

#### 教师表

Teacher(t_id,t_name) ：教师编号、教师姓名

#### 成绩表

Score(s_id,c_id,s_score) ：学生编号、课程编号、分数

### 创建表与插入数据

```sql
-- 建表
-- 学生表
CREATE TABLE `Student`(
	`s_id` VARCHAR(20),
	`s_name` VARCHAR(20) NOT NULL DEFAULT '',
	`s_birth` VARCHAR(20) NOT NULL DEFAULT '',
	`s_sex` VARCHAR(10) NOT NULL DEFAULT '',
	PRIMARY KEY(`s_id`)
);
-- 课程表
CREATE TABLE `Course`(
	`c_id`  VARCHAR(20),
	`c_name` VARCHAR(20) NOT NULL DEFAULT '',
	`t_id` VARCHAR(20) NOT NULL,
	PRIMARY KEY(`c_id`)
);
-- 教师表
CREATE TABLE `Teacher`(
	`t_id` VARCHAR(20),
	`t_name` VARCHAR(20) NOT NULL DEFAULT '',
	PRIMARY KEY(`t_id`)
);
-- 成绩表
CREATE TABLE `Score`(
	`s_id` VARCHAR(20),
	`c_id`  VARCHAR(20),
	`s_score` INT(3),
	PRIMARY KEY(`s_id`,`c_id`)
);
-- 插入学生表测试数据
insert into Student values('01' , '赵雷' , '1990-01-01' , '男');
insert into Student values('02' , '钱电' , '1990-12-21' , '男');
insert into Student values('03' , '孙风' , '1990-05-20' , '男');
insert into Student values('04' , '李云' , '1990-08-06' , '男');
insert into Student values('05' , '周梅' , '1991-12-01' , '女');
insert into Student values('06' , '吴兰' , '1992-03-01' , '女');
insert into Student values('07' , '郑竹' , '1989-07-01' , '女');
insert into Student values('08' , '王菊' , '1990-01-20' , '女');
-- 课程表测试数据
insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');

-- 教师表测试数据
insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');

-- 成绩表测试数据
insert into Score values('01' , '01' , 80);
insert into Score values('01' , '02' , 90);
insert into Score values('01' , '03' , 99);
insert into Score values('02' , '01' , 70);
insert into Score values('02' , '02' , 60);
insert into Score values('02' , '03' , 80);
insert into Score values('03' , '01' , 80);
insert into Score values('03' , '02' , 80);
insert into Score values('03' , '03' , 80);
insert into Score values('04' , '01' , 50);
insert into Score values('04' , '02' , 30);
insert into Score values('04' , '03' , 20);
insert into Score values('05' , '01' , 76);
insert into Score values('05' , '02' , 87);
insert into Score values('06' , '01' , 31);
insert into Score values('06' , '03' , 34);
insert into Score values('07' , '02' , 89);
insert into Score values('07' , '03' , 98);

```

### 插入过程

插入学生信息数据

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk8akd983cj31180pi43p.jpg)

插入课程信息数据

插入之前还是要先设置字符集（省略）

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk8ale0d14j30ts0nc0vp.jpg)

插入教师表数据

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk8aynwixrj30tu05ujsd.jpg)

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk8azfeq0jj313i0c00v1.jpg)

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk8azxbnjkj30sk0oagoc.jpg)

插入学生成绩信息数据

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk8at1uaxtj30si16q437.jpg)

