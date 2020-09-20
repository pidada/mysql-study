---
title: SQL进阶-3-排序与窗口函数
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-08-30 21:18:00
password:
summary:
tags:
  - MySQL
  - 数据库
categories:
  - MySQL
  - 进阶
---

### SQL进阶-3-排序和窗口函数

在使用数据库制作各种统计数据的时候，需要对数据进行排序，比如按照**分数、销量、人数**等数值进行排序，通常排序的方法有两种：

- 跳过之后的位次排序
- 不跳过之后的位次排序

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4nadc6dlj30bw0h4tcv.jpg)

<!--MORE-->

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8w0bft1oj30m00cmmy5.jpg)

### 窗口函数

窗口函数只在最新的`MySQL`版本中才支持的！

窗口函数只在最新的`MySQL`版本中才支持的！

窗口函数只在最新的`MySQL`版本中才支持的！

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8wzxpkhyj327y0c677o.jpg)



参考资料：https://zhuanlan.zhihu.com/p/92654574



#### 什么是窗口函数

窗口函数，也叫`OLAP`函数（Online Anallytical Processing，联机分析处理），可以对数据库数据进行实时分析处理。

#### 语法

窗口函数的基本语法：

```mysql
<窗口函数> over (partition by <用于分组的字段名>  -- partition子句可省略，不指定分组
             order by <用于排序的列名>)
```

`<窗口函数>`的位置上可以放两种函数：

- 专用窗口函数，`rank、dense_rank、row_number`等
- 聚合函数，如`sum、avg、count、max、min`等

因为窗口函数是对`where`或者`group by`子句处理后的结果进行操作，所以**窗口函数原则上只能写在select子句中**。

#### 功能

- 同时具有分组和排序的功能
- 不改变原有表的行数
- 窗口函数原则上只能写在`select`子句中

#### 实际场景

- 排名问题：每个部门按业绩来排名
- `topN`问题：找出每个部门排名前`N`的员工进行奖励

###  rank/dense_rank/row_number

#### 实例

- `rank`：并列跳跃排名
- `dense_rank`：并列连续排名
- `row_number`：连续排名

这3个函数的区别通过一个列子可以清楚地看到：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8x62y1rdj30ik0as0uv.jpg)

```mysql
select
	name,price,
	rank() over (order by price desc) as rank_1,
	dense_rank() (order by price desc) as rank_2,
	row_number() (order by price desc) as rank_3
from products;
```

#### 结论

```mysql
name         price    rank_1    rank_2￼      rank_3
橘子          100          1          1￼       1
西瓜           80          2          2￼       2
苹果           50          3          3￼       3
香蕉           50          3          3￼       4
葡萄           50          3          3￼       5
柠檬           30          6          4       6
```

总结：

- `rank()`在出现了相同位次之后，跳过了相同的位次
- `dense_rank()`则没有跳过相同的位次
- `row_number()`按照自然数的顺序进行排列

> 在上述的这三个专用窗口函数中，函数后面的括号不需要任何参数，保持()空着就可以。

### 知乎例子

#### 实现rank()

```mysql
select *,
   rank() over (partition by 班级   -- 先分组
                 order by 成绩 desc) as ranking  -- 再排序
from 班级表
```



![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi937nanr4j30x00j4ti3.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi936zjf93j30uw0m4qf1.jpg)



#### 不改变行数

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi936mivglj30s80nuzsi.jpg)



### 非等值连接实现rank()

```mysql
select p1.name,p1.price,
			(select count(p2.price)
       from products p2
       where p2.price > p1.price) + 1
       as rank_1
from products
order by rank_1;
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8xioeyfuj31pt0u078f.jpg)



- 子查询的功能是计算出比自己`(p1)`高的记录，并将其作为自己的位次
- 比如对去重之后的价格`{100,80,50}`进行分析和排序，比`100`大的个数是0，比80大的个数是1，比50大的个数是2
- `+1`之后的排名实际上就是下面将会提到的`dense_rank()`函数的排名

| 价格 | 排名 | +1   |
| ---- | ---- | ---- |
| 100  | 0    | 1    |
| 80   | 1    | 2    |
| 50   | 2    | 3    |



**如果希望排序从0开始，则去掉加1**：



![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8xma9uogj31o60tijv4.jpg)



### 非等值连接实现dense_rank()

```mysql
mysql> select p1.name, p1.price,
    (select count(distinct p2.price) from products p2 where p2.price > p1.price) + 1 as rank_1
from products p1
order by rank_1;
```



![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi8xo5x23rj31x00tw0wn.jpg)

### 使用变量实现row_number()

在`MySQL5.7.28`中实现变量实现`row_number`函数的功能

```mysql
mysql> select p.name, p.price, (@pro_rank := @pro_rank + 1) row_Number
    -> from products p,(select @pro_rank := 0) r
    -> order by price desc;
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi93ufp8noj31ib0u042a.jpg)



如果是更高级的`MySQL`版本，直接使用`row_number()`函数实现

```mysql
select name, price,
		row_number() over (partition by name order by price desc) as rowNumber
from products
```

### 聚合函数作为窗口函数

聚合窗口函数和上面提到的专用窗口函数用法完全相同，只需要把**聚合函数**写在窗口函数的位置即可

- **函数后面括号里面不能为空**
- 需要指定聚合的列名

需要在高级的`MySQL`版本或者`hive`中实现

```mysql
mysql> select *,
    -> sum(price) over (order by name) as rank_sum,
    -> avg(price) over (order by name) as rank_avg,
    -> max(price) over (order by name) as rank_max,
    -> count(price) over (order by name) as rank_count
    -> from products;
```
