---
title: SQL进阶-7-外连接使用
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-09-20 19:37:32
password:
summary:
tags:
  - 数据库
  - MySQL
categories:
  - MySQL
  - 进阶
---

### SQL进阶-7-外连接(outer join)

本文中介绍的SQL中**行列转换和嵌套式侧栏**的生成，将`SQL`语句查询的结果转换成我们想要的结果。

- 行转列，制作交叉表头
- 列转行

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4nadc6dlj30bw0h4tcv.jpg)

<!--MORE-->

### 实现行列转换（行—>列）：制作交叉表

#### 需求

根据下面的表`Courses`制作员工和课程的交叉表

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gipd43xozcj30j00dn418.jpg)

生成如下的交叉表：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gipd4ywl5bj30m00ac0uv.jpg)

#### 方法-外连接实现

```mysql
-- 水平展开求交叉表，使用外连接

select C0.name,
		case when C1.name is not null then 'o' else null end as "SQL入门",   -- 如果某位员工学习过某课程，则课程的相应列则出现他的名字，否则为NULL
		case when C2.name is not null then 'o' else null end as "UNIX基础",
		case when C3.name is not null then 'o' else null end as "java中级"
from (select distinct name from Courses) C0  -- C0是侧栏
left outer join(select name from Courses where course='SQL入门') C1 on C0.name = C1.name  -- select子句中的查询结果标为C1
left outer join(select name from Courses where course='UNIX基础') C2 on CO.name = C2.name
left outer join(select name from Courses where course='java中级') C3 on CO.name = C3.name;
```

> SQL中指定了名称的表和视图都是相当于是集合

`C0-C3`的各自内容如下：

- C0包含了全部的员工
- C1-C3每个课程学习者的集合

| name(C0) | name(C1-SQL) | name(C2-UNIX) | name(C3-java) |
| -------- | ------------ | ------------- | ------------- |
| 赤井     | 赤井         | 赤井          | 工藤          |
| 工藤     | 工藤         | 吉田          |               |
| 铃木     | 铃木         |               |               |
| 吉田     | 渡边         |               |               |
| 渡边     |              |               |               |

#### 方法-标量子查询实现

```mysql
-- 标量子查询实现
select C0.name,
	(select 'o' from Courses C1    -- 满足条件返回 o
   where course='SQL入门'
   and C1.name = C0.name) "SQL入门"，

  (select 'o' from Courses C2
   where course='UNIX基础'
   and C1.name = C0.name) "UNIX基础"，

   (select 'o' from Courses C3
   where course='java中级'
   and C1.name = C0.name) "java中级"

from (select distinct name from Courses) C0;  -- C0是表侧栏
```

**标量子查询非常方便，如果有需要可以直接添加即可**。缺点是性能不太好，性能开销还是比较大的。

#### 方法-嵌套case表达式

```mysql
-- 嵌套使用case表达式

select name,
	-- 将聚合函数的执行结果用于条件判断 : sum(case...) = 1
	case when sum(case when course='SQL入门' then 1 else null end) = 1 then 'o' else null end as "SQL入门",   -- sum的求和等于1说明这个人有选修该课程即then 'o'
	case when sum(case when course='UNIX基础' then 1 else null end) = 1 then 'o' else null end as "UNIX基础",
	case when sum(case when course='java中级' then 1 else null end) = 1 then 'o' else null end as "java中级"

from Courses
group by name;
```

### 列转成行

#### 需求1

将这张表转成行格式的数据

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gipe7j4npjj30xn08l77h.jpg)

#### union all实现

`UNION ALL`不会排除重复行的数据，所以即使吉田没有孩子，也会出现3行相关的数据

```mysql
-- 列转成行：union all

select employee, child_1 as child from personal  -- 查找child_1
union all
select employee, child_2 as child from personal  -- 查找child_2
union all
select employee, child_3 as child from personal  -- 查找child_3
```

结果为：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gipedve9qkj30sy0ukjuz.jpg)

#### 需求2

按照下面的表格生成数据

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gipetbmojaj30ik0didhz.jpg)

```mysql
-- 存储子女列表的视图（孩子主表）

create view Children(child) as select child_1 from Personnel
union
select child_2 from Personnel
union
select child_3 from Personnel;
```

```mysql
-- 员工列表为主表进行外连接操作

select emp.employee, Children.child
from Personnel emp
left outer join Children
on Children.child in (emp.child_1, emp.child_2, emp.child_3);   -- 外连接的条件通过in关键字来指定
```

### 嵌套式表侧栏

在生成统计表的工作中，经常会有制作表头和表侧栏的要求。

#### 需求

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gipf3uh06nj30m206x76g.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gipf41jlsyj30lx05hab7.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gipf48p4fgj30zk0mo7b5.jpg)

根据上面的3张表，生成如下的统计表格：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gipf4gowebj30kg0bw40m.jpg)

#### 外连接实现嵌套式表侧栏

```mysql
-- 外连接实现嵌套式表侧栏

select master.age_class as age_class,
 		master.sex_cd as sex_cd,
 		data.pop_tohoku as pop_tohoku,
 		data.pop_kanto as sex_cd
from (select age_class, sex_cd from TblAge cross join TblSex) master   -- 交叉联结生成2张主表的笛卡尔积
left outer join(select age_class, sex_cd,
                    sum(case when pref_name in ('青森', '秋田') then population else null end) as pop_tohoku,
                    sum(case when pref_name in ('东京', '千叶') then population else null end) as pop_kanto
                from TblPop
                group by age_class, sex_cd)  data  -- 临时表

 on master.age_class = data.age_class
 and master.sex_cd = data.sex_cd;
```



### 乘法运算的连接

#### 需求

商品管理表和商品销售历史管理表，**通过商品为单位汇总出各自的销量**

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gix8ue27dij30yi0rcn2f.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gix8vyh1q9j30tn0gs406.jpg)

#### 方法1

```mysql
select
	I.item_no,
	SH.total_qty
from Items I
left outer join (select item_no, sum(quantity) as total_qty   -- 2-通过外连接求解
                 from SalesHistory
                 group by item_no) SH   -- 1-先按照item_no分组求和
 on I.item_no = SH.item_no;
```

#### 方法2

```mysql
select
	I.item_no,
	sum(SH.quantity) as total_qty
from Items I
left outer join SalesHistory SH
on I.item_no = SH.item_no   -- 一对多的连接
group by I.item_no;
```

> 一对一或者一对多关系的两个集合，在进行联结操作后行数不会增加

### 外连接的3种类型

- 左外连接（left）
- 右外连接（right）
- 全外连接（full）

全外连接指的是将两个表的数据全部连接起来，如果表中不存在，则用空

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gix9ahwpobj30hu071ab9.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gix9apac8tj30gm073dgz.jpg)

```mysql
-- 使用全连接使用

select
	coalesce(A.id, B.id) as id   -- coalesce()接收多个参数，返回的是第一个非NULL的参数
	,A.name as A_name
	,B.name as B_name
from Class_A A
full outer join Class_B B
on A.id = B.id;


-- 结果
id      A_name     B_name
1       田中        田中
2       铃木        铃木
3       伊集院
4                   田园寺
```

```mysql
-- 使用左右连接+union合并实现（如果数据库不支持全外连接）

select
	A.id as id
	,A.name
	,B.name
from Class_A A
left outer join  Class_B B
on A.id = B.id
union select
				B.id as id
				,A.name
				,B.name
			from Class_A A
			right outer join Class_B B
			on A.id = B.id;
```


