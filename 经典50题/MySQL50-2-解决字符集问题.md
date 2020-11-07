---
title: MySQL50-2-解决字符集问题
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-10-29 23:51:39
password:
summary:
tags:
  - MySQL
  - 数据库
categories:
  - MySQL
  - 50题
---


### MySQL经典50题-2-解决字符集问题

在创建完数据库和表之后，需要插入模拟数据。在插入的过程中就碰到了问题，此文作为记录解决字符集的问题

- 修改MySQL字符集
- 查看库、表、字段的字符集
- 指定表、字段的字符集

<!--MORE-->

### 创建表

1. 学生表

```sql
mysql> create table Student(
    -> s_id varchar(20),
    -> s_name varchar(20) not null default '',
    -> s_birth varchar(20) not null default '',
    -> s_sex varchar(20) not null default '',
    -> primary key(s_id));
Query OK, 0 rows affected (0.02 sec)
```



![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6lti8zy8j30ss09sabm.jpg)

2. 课程表

```sql
mysql> create table Course(
    -> c_id varchar(20),
    -> c_name varchar(20) not null default '',
    -> t_id varchar(20) not null,
    -> primary key(c_id));
Query OK, 0 rows affected (0.01 sec)
```



![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6luk48u9j30qe094wfo.jpg)

3. 教师表

```sql
mysql> create table Teacher(
    -> t_id varchar(20),
    -> t_name varchar(20) not null default '',
    -> primary key(t_id));
Query OK, 0 rows affected (0.01 sec)
```



![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6lvjos7qj30qe078ab0.jpg)

4. 成绩表

```sql
mysql> create table Score(
    -> s_id varchar(20),
    -> c_id varchar(20),
    -> s_score int(3),
    -> primary key(s_id,c_id));
Query OK, 0 rows affected (0.00 sec)
```



![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6lwfbvdkj30m607wq3y.jpg)

### 报错解决

#### 报错原因

在创建了表之后直接往Student表中插入数据，报错了：

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6m5pspmjj31eq03ywfd.jpg)



网上找到了原因：**库、表和字段的字符集不同造成的**



![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6mo9cobvj30ts0na79c.jpg)

#### 解决方案

**如何查看数据集**

查看mysql的字符集：

```sql
show variables where Variable_name like '%char%';
```

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6mtf5a22j31cy0icmzv.jpg)

查看某一个数据库字符集：

```sql
show create database test;
```

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6mrri2gqj318609cdh5.jpg)

查看某一个数据表字符集：

```sql
show create table Student;
```

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6msh0l6sj312c0mqtbg.jpg)



具体解决步骤：

1. 修改数据库和表的字符集

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6mugxio1j30uk09kmyx.jpg)

2. 指定字段的字符集

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6mwbu1bzj31ca05iwfq.jpg)

3. 字符集相同

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6mwvkm96j30pq0n2mza.jpg)



最终成功插入数据：

![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk6mxiffiij31do04agm6.jpg)

### 修改MySQL字符集

```sql
mysql> set character_set_client=utf8;
mysql> set character_set_connection=utf8;
mysql> set character_set_database=utf8;
mysql> set character_set_results=utf8;
mysql> set character_set_server=utf8;
mysql> set character_set_system=utf8;
mysql> set collation_connection=utf8;
```


