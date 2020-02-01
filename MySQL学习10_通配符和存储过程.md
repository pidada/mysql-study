---
title: MySQL学习10_通配符和存储过程
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-05 10:46:08
password:
summary:
copyright: true
tags:
  - 通配符
  - like
  - 存储过程
categories:
  - MySQL
  - 基础
---

### 通配符

> 通配符的主要作用是对特殊的数据进行搜索，它是用来匹配一部分特定字符。通配符只能用于文本数据，非文本数据不能使用通配符。

[菜鸟课程](https://www.runoob.com/w3cnote/mysql-stored-procedure.html)

#### 搜索模式

由字面值、通配符或者两者组成的搜索条件。

#### %

- `%`表示任意字符出现任意次数，包含0个，1个或者多个字符
- `%`不能匹配`NULL`

<!--MORE-->

```sql
select prod_id, prod_name
from Products
where prod_name like 'Fish%'  -- 查询Fish开头
where prod_name like '%Fish'  -- 查询Fish结尾
where prod_name like '%Fish%' -- 查询包含Fish，两边是任意字符
```



#### 下划线_

下划线通配符是只能匹配单个字符，而不是多个字符。一个`_`只能匹配一个字符，不能多也不能少

```sql
select prod_id, prod_name
from Products
where prod_name like '_ inch teddy bear';   -- 找出个位数的teddy bear
where prod_name like '__ inch teddy bear';  -- 两个下划线，找出两位数的teddy bear
```

#### 方括号`[]`

方括号`[]`通配符用来匹配一个字符集，它必须指定匹配的位置。用来匹配方括号里面的一个内容即可。

表示否定含义的 符号是前缀符号`^`，表示`取反操作`

```sql
select cust_contact
from Customers
where cust_contact likt '[JM]%'     -- 表示匹配以J或者M开头的内容，后面是任意字符
where cust_contact likt '[^JM]%'    -- ^表示取反
where not cust_contact likt '[JM]%' -- 效果等同于^取反操作
order by cust_contact;
```

#### 通配符使用技巧

- 不要过度地使用通配符
- 不要搜索模式置于开始处
- 注意任意通配符的位置

### 存储过程

> 存储过程`Stored Procedure`，是为以后可以使用而保存的一条或者多条`SQL`语句。它是一种在数据库中存储复杂程序，以便外部程序调用的一种数据库对象。

#### 为什么需要存储过程

- 简化操作：将处理封装在一个单元中，简化复杂的操作，隐藏赋复杂的业务逻辑
- 防止错误：不必要建立重复的一系列处理步骤，保证数据的一致性
- 保证安全：简化对变动的管理。通过存储过程限制对数据库的访问，减少数据讹误。
- 提高性能：存储过程通常是以`编译`的形式存在，`DBMS`处理命令需要的时间少，提高性能
- 不同的`DBMS`对存储过程的语法有所不同，一般不能移植

**总结：简单，安全，高性能**

#### 执行存储过程

执行存储过程使用的`execute`关键字，它接受存储过程名和需要传递的参数，一个栗子：

```sql
execute addNewProduct(   -- 执行该名字的存储过程
    'JS01',   --供应商
    'Shufujia',   -- 商品名字
    7.08,   -- 价格
    'a product of skin' -- 描述
);
```

存储过程自动将新的行添加到`Products`表中，并且将传入的属性传给对应的列。



#### 创建存储过程

```sql
CREATE
    [DEFINER = { user | CURRENT_USER }]
　PROCEDURE sp_name ([proc_parameter[,...]])
    [characteristic ...] routine_body
 
proc_parameter:
    [ IN | OUT | INOUT ] param_name type   -- 3个主要的参数
 
characteristic:
    COMMENT 'string'
  | LANGUAGE SQL
  | [NOT] DETERMINISTIC
  | { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
  | SQL SECURITY { DEFINER | INVOKER }
 
routine_body:
　　Valid SQL routine statement
 
[begin_label:] BEGIN
　　[statement_list]
　　　　……
END [end_label]
```

声明语句结束符：

```sql
DELIMITER $$   -- 两者皆可
delimiter // 
```

声明存储过程

```sql
CREATE PROCEDURE demo_in_parameter(IN p_in int)
```

存储过程开始和结束符号:

```sql
BEGIN .... END    
```

变量赋值:

```sql
SET @p_in=1  
```

变量定义:

```sql
DECLARE l_int int unsigned default 4000000; 
```

创建mysql存储过程、存储函数:

```sql
create procedure 存储过程名(参数)
```

存储过程体:

```sql
create function 存储函数名(参数)
```

#### 栗子

创建某个数据库用于实例操作

```sql
mysql> create database db1;   -- 创建库
mysql> use db1;      -- 使用库
mysql> create table PLAYERS as select * from TENNIS.PLAYERS;
mysql> create table MATCHES  as select * from TENNIS.MATCHES;
```

存储过程实例

```sql
mysql>delimiter $$    -- 将SQL语句的结束符临时改为$$(这个符号可以自定义)
mysql>create procedure delete_matches(in p_playerno integer)
->begin
->    delete from matches
->    where playerno = p_playerno;
->end$$

mysql>delimiter ;   -- 结束符号改回;
```

