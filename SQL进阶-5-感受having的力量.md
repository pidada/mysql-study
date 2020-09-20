---
title: SQL进阶-5-感受having的力量
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-09-08 00:26:45
password:
summary:
tags:
  - MySQL
  - 数据库
categories:
  - MySQL
  - 进阶
---

### SQL进阶-5-感受having的力量

`HAVING`是`SQL`中一个非常重要的功能，本文中将会介绍`SQL`中`HAVING`子句的用法。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4nadc6dlj30bw0h4tcv.jpg)

<!--MORE-->


### SQL语句执行顺序

1. select
2. from
3. where
4. group by
5. having
6. order by（desc是降序）

从上面的顺序中可以看出来，**HAVING子句是在group by 分组之后再执行的**



### 案例1-寻找缺失的编号

#### 需求

下面的数据中缺少4和7

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1giag7axg2rj30gk0bv0ug.jpg)

#### 判断是否缺失

```mysql
select '存在缺失的编号' as gap
from tableName
having count(*) <> max(seq);

-- 结果

gap
-----
'存在缺失的编号'
```

> 查询结果中出现了一行，说明存在缺失值；若一行都没有，则统计的总行数刚好是最大的seq值

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1giagd96lkgj30rr0ayq6j.jpg)

**从上面的语句中可以看出来，having子句是可以单出使用的**，注意两点：

- `select`子句中不能使用原有表的列了
- 使用常量（示例）或者使用聚合函数

#### 解决-缺失的最小编号

先通过上面的语句确定真的是存在缺失值，再通过👇的语句来锁定缺失值的最小编号

```mysql
select min(seq+1) as gap
from tableName
where (seq+1) not in (select seq from tableName);   -- 检查比某个编号大1的编号是否在表中

-- 结果

gap
---
4
```

如何理解上面👆的SQL语句？

**seq值+1不在表中**

| 编号seq | 编号seq+1 | 是否在数据中 |
| ------- | --------- | ------------ |
| 1       | 2         | 是           |
| 2       | 3         | 是           |
| 3       | 4         | 否           |
| 4       | 5         | 是           |
| 5       | 6         | 是           |
| 6       | 7         | 否           |
| 7       | 8         | 是           |

**所以缺失值的最小编号是4（3+1）**

### 案例2-求众数

简单地求平均值的缺点就是，数据很容易受到离群值（outlier）的影响，此时使用**反映群体趋势的指标-众数mode**更加合理。

> 众数指的是在数据中出现次数最多的数

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gih5c2x72uj30em0im0w2.jpg)

#### 解法1-使用all



```mysql
-- 求众数的SQL语句：使用谓词
select income, count(*) as number
from Graduates
group by income
having count(*) >= all(select count(*) -- 大于根据income分组的每个值
                       from Graduates
                       group by income)
```

#### 解法2-max函数

使用ALL谓词在数据中存在空集或者NULL的时候会出现问题，使用极值函数代替



```mysql
select income,count(*) as number
from Graduates
group by income
having count(*) >= (select max(cnt)
                    from(select count(*) as cnt   -- 分组后的最大值，整个表当做临时表
                         from Graduates group by income)temp
                   )
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gih5vqrf2xj323k0u042t.jpg)

### 案例3-求中位数（having自连接）

**中位数：指的是将集合中的元素按照升序排列后恰好位于正中间的数据**

#### 做法

将集合按照大小排列后，让两个子集同时拥有正中间的元素，**共同部分的元素的平均值就是中位数**

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gih5ybweeej30yk0co79j.jpg)

```mysql
-- 求解中位数:having 子句中使用非等值连接
-- 加上等号是为了解决个数是偶数的情况

select avg(distinct income)
from (select t1.income from Graducts t1, Graducts t2 group by income
      having sum(case when t2.income >= t1.income then 1 else 0 end) >= count(*) / 2   -- 让两个子集拥有公共的部分，否则个数为偶数的时候没有相同的部分
      and sum(case when t2.income <= t1.income then 1 else 0 end) >= count(*) / 2
     )temp   -- 创建临时表
```

```mysql
-- 求解中位数
select avg(distinct price)
from (select t1.price from products t1, products t2 group by price
      -- S1的条件
      having sum(case when t2.price >= t1.price then 1 else 0 end) >= count(*) / 2
      -- S2的条件
      and sum(case when t2.price <= t1.price then 1 else 0 end) >= count(*) / 2
     )temp;   -- 创建临时表
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gih6bp9tqmj313c0u079u.jpg)

### 关于count

`count(*)`：可以查询`NULL`的情况，查询的是所有行的数目

`count(列名)`：和其他的聚合函数相同，需要先排除`NULL`的情况

#### 需求

找出哪些学院的学生全部提交了？？？

> 答案：理学院和经济学院，文学院和工学院有NULL的情况

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gih6lw74uhj30tp0dkgq7.jpg)

#### 解决

1. 如果两个`count`方式的值相等，说明该列没有缺失值

```mysql
select dpt
from Studuents
group by dpt
having count(*) = count(sbmt_date);  -- 如果两个count方式的值相等，说明该列没有缺失值
```

2. **使用case解决**：case表达式相当于是进行判断的函数，用来判断各个元素是否满足了某个条件的集合

```mysql
select dpt
from Students
group by dpt
having count(*) = sum(case when sbmt_date is not null then 1 else 0 end);  -- 当不是NULL的个数等于count(*)的个数，说明没有缺失值
```

### 购物篮分析

#### 需求1-带余除法

在下面的两个表中，插叙包含所有items中所有商品的店铺：**item中的商品在店铺中全部存在**，结果是$\color{red}{仙台和东京}$



**表Items和表shopitems**



![](https://tva1.sinaimg.cn/large/007S8ZIlgy1giij1tm7nlj307706zq3o.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1giij218nulj30eu0ijq5w.jpg)

#### 解决-使用having语句

```mysql
select SI.shop
from ShopItems SI, Items I
where SI.item = I.item  -- 指定两个表的连接点
group by SI.shop  -- 分组
having count(SI.item) = (select count(item)
                         from Items);  -- 结果是常量3，表示两个表连接后结果是3行的店铺将被选中
```

- 先连接：两个表中的item必须相同，也是就说ShopItems中某个店铺的item必须包含Items表中的3个名字
- 对应数据：仙台4个名字，其中3个已经包含了items表中的数据；东京刚好3个都是Items中的数据

#### 需求2-精确关系除法

在上面的例子中，**仙台有4个item也被选中了，如何将其排除，只选择东京**，刚好3个item的店铺呢？？？这种问题称之为**精确关系除法**

```mysql
-- 精确关系除法：使用外连接和count函数

select SI.shop
from shopitems SI
left outer join items I
on SI.item = I.item   -- 两个表的连接操作
group by SI.shop
having count(SI.item) = (select count(item) from items)   -- 保证两个表的shop中item的个数刚好相等，都是3，排除仙台
and count(I.item) = (select count(item) from items);   -- 排除大阪，只有2个，NULL不算
```

两个表外联结的结果为：

> 因为是left连接，所以ShopItems中的数据全部保留，Item中的数据没有则为NULL

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1giijqmpymlj30qw0iiq7v.jpg)


