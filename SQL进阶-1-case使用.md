---
title: SQL进阶-1-case使用
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-08-27 00:43:54
password:
summary:
tags:
  - 数据库
  - MySQL
categories:
  - MySQL
  - 进阶
---


### SQL进阶-1-case语句

认真把`SQL`语言提升下，选择了`日本`的一本书籍。看过几本其他日本的IT人员写的书籍，发现他们好像有一个共同的特点，也是自己很喜欢而且一直坚持在使用的一点：`多用图，将文字和图形结合起来`，比如：

- 《图解HTTP》
- 《图解机器学习》
- 《图解网站分析》
- 《图解TCP/IP》
都是将图像运用的淋漓尽致，看完之后很多知识点都会理解的更加透彻和记忆的更加深刻。

**一般而言，日本人写的书比较实在**

所以自己选择了这本《**SQL进阶教程**》，也是图文并茂的。本文中记录的是关于`case`语句的用法

- 行列转换
- 已有数据的重分组和分类
- 与约束的结合使用
- 针对聚合结果的条件分支





![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4nadc6dlj30bw0h4tcv.jpg)

<!--MORE-->

### 2种方式

- 简单case表达式
- 搜索case表达式

```mysql
-- 简单
case sex
	when '1' then '男'   -- then后面表示结果
	when '2' then '女'
else '其他' end

-- 搜索
case when sex='1' then '男'
	when sex='2' then '女'
else '其他' end
```

> 简单表达式能够写的搜索表达式都能写

### 注意事项

- 统一分支返回的数据类型
- 不要忘记写end
- **养成写else子句的习惯**，虽然else子句是可选。如果不写，结果自动变成NULL

### 案例1-统计分组求和

#### 需求

将表1的数据分组统计成表2，表1：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4nllz04oj30ij0gdju5.jpg)

表2：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4nmntzz0j30ab06rt9m.jpg)

#### SQL实现

```mysql
-- 县编号转成地区编号

-- 方式1
select case pref_name
		when '德岛' then '四国'
		when '香川' then '四国'
		when '爱媛' then '四国'
		when '高知' then '四国'

		when '福冈' then '九州'
		when '佐贺' then '九州'
		when '长崎' then '九州'
	else '其他' end as district,  -- 别名
	sum(population)
from poptable
group by case pref_name   -- 将case表达式的内容直接复制到这里，担任分组的元素
			when '德岛' then '四国'
			when '香川' then '四国'
			when '爱媛' then '四国'
			when '高知' then '四国'

			when '福冈' then '九州'
			when '佐贺' then '九州'
			when '长崎' then '九州'
		else '其他' end;

-- 方式2--->非常好用
select case pref_name
			when '德岛' then '四国'
			when '香川' then '四国'
			when '爱媛' then '四国'
			when '高知' then '四国'

			when '福冈' then '九州'
			when '佐贺' then '九州'
			when '长崎' then '九州'
		else '其他' end as district,  -- 别名
		sum(population)
from poptable
group by district;   -- 此处直接使用别名


```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4o2zada2j30zk0gywoi.jpg)

在有些数据库是不支持这种写法，但是在mysql中是可以顺利执行的：

- 先对select子句里面的列表进行扫描
- 再对列进行计算

**不推荐使用**！！！！！

### 案例2-数字大小分类

表格同上

#### 需求

将不同的数字按照类别进行分组表示

#### SQL实现

```mysql
-- 按照人口数量等级划分
select case when population < 100 then '01'
			when population >=100 and population < 200  then '02'
			when population >=200 and population < 300  then '03'
			when population >=400 then '04'
		else NULL end as pop_class,
		count(*) as cnt
from poptable
group by case when population < 100 then '01'
			when population >=100 and population < 200  then '02'
			when population >=200 and population < 300  then '03'
			when population >=400 then '04'
		else NULL end;
```

### 案例3-不同条件统计（男女）

#### 需求

将表1统计成表2：根据县的不同，来统计每个县的男女人数

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4o6w5jw4j30m40sz0y0.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4o7dq31ij30bx0fyjtn.jpg)

#### SQL实现

比较传统的做法是直接通过2条SQL语句来实现

```mysql
select pref_name,   -- 统计每个县的男性人数
		sum(population)
from poptable
where sex='1'
group by pref_name;

select pref_name,  -- 统计每个县的女性人数
		sum(population)
from poptable
where sex='2'
group by pref_name;
```

case实现：将行结构的数据变成了列结构的数据

```mysql
select pref_name,
		-- 男性人口
		sum(case when sex='1' then population else 0 end) as cnt_m,
		sum(case when sex='2' then population else 0 end) as cnt_f
from poptable
group by pref_name
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4o7dq31ij30bx0fyjtn.jpg)

### 约束constraint

将case表达式和check约束结合起来，比如需求：女性员工的工资必须低于20万日元

```mysql
constraint check_salary check (
		case when sex='2'  -- 指定女性
            then case when salary < 2000000 
				then 1 -- 低于20万
				else 0 end
            else 1 end = 1
                              )
```

### 逻辑与蕴含式

在蕴含式中，要想`P--->Q`为真，需要PQ同时为真，或者P为假，或者P的真假无法判定

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4olwlx7sj30ec0htwh6.jpg)

### 案例4-update中使用case进行条件分支

#### 需求

```mysql
1．对当前工资为30万日元以上的员工，降薪10%。
2．对当前工资为25万日元以上且不满28万日元的员工，加薪20%
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi4opihupkj30dh08q75u.jpg)

#### SQL实现

```mysql
update salaries
		set salary = case when salary >= 300000 then salay * 0.9
						when salary >= 250000 and salary < 280000 then salary * 1.2
					else salary end;   -- 不在上面的两个范围内保持不变，这个一定要写不然会变成NULL
```

### 使用case调换主键值

```mysql
update someTable
		set p_key = case when p_key = 'a' then 'b'
						when p_key = 'b' then 'a'
					else p_key end
		where p_key in ('a', 'b')
```

### 案例5-case嵌套子查询

#### 需求

通过两张表生成第三张交叉表：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi5sotax8oj30dy08cabh.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi5spm094pj30al0bkabh.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi5sq81zhjj30ry0dutbe.jpg)

#### SQL实现

```mysql
-- in 实现
select course_name,
	case when course_id in (
    		select course_id from opencourses
    		where month=200706) then 'o'  -- 6月份画o
   		else '*' end as '6月',
  case when course_id in (
    		select course_id from opencourses
    		where month=200707) then 'o'
   		else '*' end as '7月',
  case when course_id in (
    		select course_id from opencourses
    		where month=200808) then 'o'
    	else '*' as '8月'
from coursemaster
```



```mysql
-- exists实现

select CM.course_name,
		case when exists (
      select course_id from OpenCourse OC 
      where month = 200706
      and OC.course_id = CM.course_id)
    then 'o'
    else '*' end as "6月",
    
    case when exists (
      select course_id from OpenCourse OC 
      where month = 200707
      and OC.course_id = CM.course_id)
    then 'o'
    else '*' end as "7月",
    
    case when exists (
      select course_id from OpenCourse OC 
      where month = 200708
      and OC.course_id = CM.course_id)
    then 'o'
    else '*' end as "8月",
from CourseMaster CM;
```

特点

- 这样的查询方式没有进行聚合，不需要排序
- 从性能上说，exists更好

### 案例6-case中使用聚合函数

#### 需求

关于学生社团的表格：

- 只参加一个社团的学生的**社团ID**
- 参加了多个社团的学生的**主社团ID**，Y表示是主社团**

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi5t4zi7fvj30zk0een2f.jpg)

#### SQL实现

```mysql
-- 加入了一个社团
select std_id, max(club_id) as main_club
from StudentClub
group by std_id
having count(*) = 1;


-- 加入了多个社团的主ID
select std_id, club_id as main_club
from StudentClub
where main_club_flag='Y';  -- 加入了多个社团的主ID
```

#### Case 语句实现

```mysql
select std_id,
			case when count(*) = 1    -- 只加入了一个社团
			then max(club_id)   -- 直接取出最大的ID号
			else max(case when main_club_flag = 'Y'   -- 指定加入了多个社团
              then club_id  -- 取出此时主社团对应的ID号
              else NULL end)   -- 一定要有else语句
      end as main_club
from StudentClub 
group by std_id;
```

> 新手使用having进行分支，熟练之后可以使用select语句进行分支

### 总结

- `case`表达式可以写在`select`子句里时，可以写在聚合函数内部，也可以是外部
- 能够写列名和常量的位置，都可以使用`case`语句
- `case`表达式可以写在`select、group by、having、where、order by`子句中
- `case`表达式是一种表达式，而不是语句，具有更好的可移植性




