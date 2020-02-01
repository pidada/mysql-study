---
title: MySQL学习12_事务
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-07 09:10:12
password:
summary:
copyright: true
tags:
  - MySQL
  - 事务
categories:
  - MySQL
  - 进阶
---

### 什么是事务

#### 概念

> 事务`Transaction`，一个最小的、不可再分的工作单元，通常一个事务对应一个完整的业务。`InnoDB`引擎是支持事务的，`MyISAM`不支持事务。事务是针对数据库中`DML`数据操作语言的。

- **在MySQL中，一条SQL语句就是一个事务。**
- 一个完整的业务需要大量的`DML（insert、update、delete）`语句来共同完成。只有`DML`数据操作语句才有事务。
- 事务保证一组`SQL`语句要么全部执行成功，要么全部失败，以此来维护数据库的完整性。

[MySQL 四种事务隔离级的说明](https://www.cnblogs.com/zhoujinyi/p/3437475.HTML)

<!--MORE-->

#### 栗子：银行转账业务

比如在两个表中，A（原有400）给B（原有200）转200块钱，包含两个过程：A转出200，B转进200，只有当两个过程全部完成才算真正的执行了一个完整的事务过程。

```bash
update user set fee=200 where id=1;     # 语句1
# 由于断网、安全限制、超出磁盘空间等不可控制原因，下面的语句可能无法成功执行
update user set fee=400 where id=2;     # 语句2
```

- 语句1的成功执行，并不能将底层数据库中的第一个账户的数据进行修改，只是单纯地记录操作，记录在**内存**中完成
- 第二条语句成功执行之后，和底层数据库文件中的数据完成同步
- 若第二条数据执行失败，清空所有的历史记录

------

### MySQL服务器逻辑架构图

![](https://s2.ax1x.com/2019/10/07/u2i7Sx.md.png)

### 事务相关术语

开启事务：`start transaction`

事务结束：`end transaction`

提交事务：`commit transaction`

	- `MySQL`语句是直接针对数据库表操作的，写或保存等操作都是自动进行，这是所谓的`隐式提交implicit commit`
	- 事务处理模块中，提交不会隐式的进行，需要使用`commit`语句

回滚事务：`rollback transaction`

保留点：`savepoint`，事务处理的过程中设置的临时占位符`placeholder`，可以对其发布回退。

	- 保留点越多越好：越多的话，能够更加灵活地处理回退问题
	- 释放保留点：保留点在事务处理完成（执行一条 `rollback`或者`commit`）后自动释放，也可以通过`release savepoint`释放。

> - 执行成功：只有进行了commit操作，数据才会从内存中写入磁盘中
>
> - 隐式事务关闭：提交或者回滚操作之后，事务会自动地关闭

```sql
CREATE DATABASE IF NOT EXISTS employees;    -- 创建数据库
USE employees;

CREATE TABLE `employees`.`account` (   -- 数据库中创建表account
  `id` BIGINT (11) NOT NULL AUTO_INCREMENT,
  `p_name` VARCHAR (4),
  `p_money` DECIMAL (10, 2) NOT NULL DEFAULT 0,
  PRIMARY KEY (`id`)  -- 设置主键
) ;
INSERT INTO `employees`.`account` (`id`, `p_name`, `p_money`) VALUES ('1', 'tim', '200');    -- 插入两条记录
INSERT INTO `employees`.`account` (`id`, `p_name`, `p_money`) VALUES ('2', 'bill', '200'); 

START TRANSACTION;   -- 开启事务
SELECT p_money FROM account WHERE p_name="tim";    -- 三条语句必须完整执行 
UPDATE account SET p_money=p_money-100 WHERE p_name="tim";
UPDATE account SET p_money=p_money+100 WHERE p_name="bill";
COMMIT TRANSACTION;   -- 显式提交
```



### 事务特点

#### 四大特性

> 事务具有四大特点，简称为`ACID`：

- 原子性`Atomicity`：一个事务中的语句，要么全部成功，要么全部失败。不存在只执行了部分的情况。
- 一致性`Consistency`：在事务开始之前或者结束之后，必须保持数据库的一致性。比如上面的栗子中，A减掉200，那么相应的，B一定要加上200。否则数据库中的数据不一致。
- 隔离性`Isolation`：当多个用户并发访问数据库，操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。用户的操作之间存在独立性。**事务A和B之间具有隔离性。**
- 持久性`Durability`：事务一旦被提交，对数据库中的数据的改变就是永久性的，即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作。**事务的成功，是硬盘数据上的更改**，不仅是内存上的变化。**持久性是事务的保证，是事务结束的标志。**

#### 隔离级别

| 隔离级别                     | 脏读(Dirty Read) | 不可重复读（NonRepeatable Read） | 幻读（Phantom Read） | 加锁读Locked Read |
| ---------------------------- | ---------------- | -------------------------------- | -------------------- | ----------------- |
| 未提交读（Read uncommitted） | Y                | Y                                | Y                    | N                 |
| 已提交读（Read committed）   | N                | Y                                | Y                    | N                 |
| 可重复读（Repeatable read）  | N                | N                                | Y                    | N                 |
| 可串行化（Serializable ）    | N                | N                                | N                    | Y                 |

- 脏读：未提交读，事务中修改即使没有提交，对其他会话也是可见的，可以读取到未被提交的数据。脏读会导致很多的问题，较少使用
- 提交读：不可重复读，该级别保证事务如果没有成功执行（`commit`），事务中的操作不会被其他会话可见。解决了脏读问题，但是会对其他`session`产生两次不一样的读取结果
- 幻读：会话T1事务中执行一次查询，然后会话T2新插入一行记录，**这行记录恰好可以满足T1所使用的查询的条件**。然后T1又使用相同 的查询再次对表进行检索，但却看到了事务T2刚才插入的新行。这个新行就称为“幻像”。



#### 查看隔离级别

```sql
-- 查看系统隔离级别：
select @@global.tx_isolation;

-- 查看当前会话隔离级别
select @@tx_isolation;

-- 设置当前会话隔离级别
SET session TRANSACTION ISOLATION LEVEL serializable;   -- serializable 级别

-- 设置全局系统隔离级别
SET GLOBAL TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;  -- READ UNCOMMITTED 可读未提交级别
```



------

### 事务开始和结束标志

#### 开始标志

任何一条DML语句的执行，标志事务开始

#### 结束标志

- `commit`：提交
  - 成功的结束
  - 将所有的`DML`语句的操作历史记录和底层硬盘中的数据进行了同步。
  - 只有事务成功执行，**硬盘**中的数据才会进行修改更新。
- `rollback`：回滚
  - 失败的结束
  - 将所有的`DML`语句的操作记录进行全部清空。