---
title: SQL进阶-4-查找重复行数据
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-08-30 23:59:10
password:
summary:
tags:
  - MySQL
  - 数据库
categories:
  - MySQL
  - 进阶
---

### SQL进阶-4-如何查找重复行数据

有时候数据库中表的数据可能存在重复的情况，如何从表中找出重复的数据呢？本文中提到两种方式：

- 使用group by + 临时表

- 使用group by + having

  ![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4nadc6dlj30bw0h4tcv.jpg)

<!--MORE-->

### 需求

根据价格price找出相同数据的行记录

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi97nafb0vj30r40pyach.jpg)

### 方式1-临时表实现

1. 先统计每个price出现的次数，次数大于1则肯定是重复的

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi97peg8fdj30qk0jcwg1.jpg)

2. 将上面的结果看做是一个临时表，从临时表中直接取出重复的行记录

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi97u25jmfj31ct0u0n2w.jpg)

**从原始数据中看出来只有价格50和100具有重复值**



### 方式2-使用having

```mysql
mysql> select price,count(*)
    -> from products
    -> group by price
    -> having count(*) > 1;  -- 直接指定条件
```



![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi97zpnxodj30pa0j2wg2.jpg)

### 重复出现n次的数据

```mysql
mysql> select price, count(*)
    -> from products
    -> group by price
    -> having count(*) > n;  -- 直接指定条件
```



### 需求-提取重复行的全部数据

```mysql
select p.id, p.price, p.name
from products p
join (select price, count(*) as number
      from products
      group by price
      having count(*) > 1) as temp  -- 将临时表的price 和 原始表的price进行联结，查询原始表的全部数据
on p.price=temp.price;
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi985gflfsj32380setdb.jpg)



### SQL语句执行顺序

1. select
2. from
3. where
4. group by
5. having
6. order by（desc是降序）
