---
title: MySQL学习3_数据类型、字段及运算符
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-01 15:36:26
password:
summary:
copyright: true
tags:
  - MySQL
  - 数据库
categories:
  - MySQL
  - 基础
---

本篇博文中主要是介绍`MySQL`数据库中的数据类型和字段、运算符的相关知识

### 数据类型

`MySQL`数据库中的主要数据类型有四种：

- 数值类型
- 浮点型
- 字符串类型
- 日期时间类型

#### 数值类型

- 一般情况下：用`int`就可以
- 超过了最大值，以最大值为准

<!--MORE-->

![img](https://s2.ax1x.com/2019/10/01/uUFVNd.md.png)

#### 浮点类型

- `float(m,d)`：单精度，8位精度；m表示总个数，d表示小数位
- `double(m,d)`：双精度，16位精度；m表示总个数，d表示小数位
- `decimal(m,d)`：定点数，m表示总长度，d表示小数位

![img](https://s2.ax1x.com/2019/10/01/uUFlDS.md.png)

#### 字符串类型

用的最多的是`char`和`varchar`以及`text`，其中

- `char`：定长，不够补空格，多了减掉；比如`char(10)`：输入`12345678**`，后面自动补两个空格。特点：**存取速度快，占空间**
- `varchar`：边长，根据实际自动改变长度。特点：**存取速度慢，节省空间**
- `text`：主要是用于存储文本信息

| 函数        | 作用                     | 所占字符                          |
| ----------- | ------------------------ | --------------------------------- |
| length      | 返回字符串所占的`字节数` | `汉字占3个`； 数字和字母占1个字符 |
| char_length | 返回字符串所占的`字符数` | 汉字、数字、字母都是一个字符      |

![img](https://s2.ax1x.com/2019/10/01/uUFhDO.md.png)

#### 日期时间类型

**建议使用int存储时间戳(10位数字)的形式**

![img](https://s2.ax1x.com/2019/10/01/uUFfKK.png)

### 字段名修饰

字段指的是表中第一行即列属性，用`column`表示。常用来修饰字段的有：

![](https://s2.ax1x.com/2019/10/01/uUFvqS.png)

| 修饰语         | 作用                             |
| -------------- | -------------------------------- |
| unsigned       | 无符号                           |
| auto_increment | 自增                             |
| default        | 默认值                           |
| comment        | 字段解释说明                     |
| not null       | 非空                             |
| null           | 空                               |
| unique         | 唯一索引                         |
| index          | 普通索引                         |
| primary key    | 主键，必须指定为`auto_increment` |



**索引的作用是加快查询速度**

### 运算符

常用的运算符有：

- = ：表示赋值或者判断
- !=或者<>：不等于
- <：小于
- \>：大于
- <=、>=：小于等于或者大于等于
- OR：或
- AND：与
- BETWEEN...AND...：在...和...之间
- IN/NOT IN ：在或不在