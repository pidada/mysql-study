---
title: sqlzoo练习13-join-quiz2
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-30 18:48:39
password:
summary:
tags:
  - join
  - sql
  - sqlzoo
categories:
  - MySQL
  - sqlzoo练习
---

### sqlzoo练习13-join-quiz2

还是通过下面的`3`张表进行练习

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbdq721323j316x0u0guy.jpg)



<!--MORE-->

### 练习

1. Select the statement which lists the unfortunate directors of the movies which have caused financial loses (gross <  budget)

> 找出毛利小于预算的导演
>
> 需要注意的是导演们的名字也是在actor表中的

```sql
select name
from actor
inner join movie on actor.id=director    -- actor表中的id和director相同即可
where gross < budget;
```

2. Select the correct example of JOINing three tables

> 3个表的联结通过两个Join实现：`actor—>casting—>movie`

```sql
select *
from actor
	join casting on actor.id=actorid
	join movie on movie.id=movieid;
```

3. Select the statement that shows the list of actors called 'John' by order of number of movies in which they acted

> 找出由名字中包含`John`的演员的电影（通配符的使用），根据电影数量排序
>
> 笔记：`order by` 排序的时候可以使用字段名字，也可以**使用字段的相对位置**

```sql
select name, count(movieid)   -- 统计数量
from casting
	join actor on actorid=actor.id  -- 两个表的联结
where name like 'John %'
group by name   -- 名字分组
order by 2 desc   -- 2 表示的是第2个字段count(movieid)
```

4. Select the result that would be obtained from the following code:

> 选择由该演员出演主角的电影

```sql
select title
from movie
	join casting on (movieid=movie.id)
	join actor on   (actorid=actor.id)
where name='Paul Hogen'
and ord=1
```

![image-20200130183611907](https://tva1.sinaimg.cn/large/006tNbRwgy1gbert23nymj30fo08k3z0.jpg)

5. Select the statement that lists all the actors that starred in movies directed by Ridley Scott who has id 351

> 找出由RS导演的电影的主演

```sql
select name
from movie
	join casting on movie.id=movieid
	join actor on actor.id=actorid
where ord=1  -- 主演
and director = 351   -- 导演编号
```

6. There are two sensible ways to connect movie and actor. They are:

> 两种方式将`movie`和`actor`表进行联结

- 通过director字段和actor表中的主键相连接
- 通过casting表将另外两个表连接

![image-20200130183908556](https://tva1.sinaimg.cn/large/006tNbRwgy1gberw2id70j30rg04igm4.jpg)

7. Select the result that would be obtained from the following code:

通过代码选出正确答案

```sql
select title, yr
from movie, casting, actor
where name='Robert De Niro'
and movieid=movie.id
and actorid=actor.id
and ord=3   -- 排名第3
```

![image-20200130184344783](https://tva1.sinaimg.cn/large/006tNbRwgy1gbes0vp79hj30ea06wmxk.jpg)



> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbdq721323j316x0u0guy.jpg)
