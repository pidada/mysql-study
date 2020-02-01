---
title: sqlzoo练习12-more-join
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-29 21:03:11
password:
summary:
tags:
  - sql
  - sqlzoo
categories:
  - MySQL
  - sqlzoo练习
---

### sqlzoo练习12-More Join

This tutorial introduces the notion of a join. The database consists of three table`movie`,  `actor`and `casting`  .

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbdq721323j316x0u0guy.jpg)



<!--MORE-->



> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gb5oefiwonj30qu08u3z1.jpg)



>![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbdq6salz0j30vw0ji0ue.jpg)



[more-join](https://sqlzoo.net/wiki/More_JOIN_operations)

### 练习-基础

1. List the films where the **yr** is 1962 [Show **id**, **title**]

```sql
select id, title
from movie
where yr=1962;
```

2. Give year of 'Citizen Kane'.

```sql
select yr
from movie
where title='Citizen Kane';
```



3. List all of the Star Trek movies, include the **id**, **title** and **yr** (all of these movies include the words Star Trek in the title). Order results by year.

```sql
select id, title, yr
from movie
where title like '%Star Trek%'
order by yr;
```

4. What **id** number does the actor 'Glenn Close' have?

```sql
select id
from actor
where name='Glenn Close';
```

5. What is the **id** of the film 'Casablanca'

```sql
select id
from movie
where title='Casablenca';
```

6. Obtain the cast list for 'Casablanca'.

Use **movieid=11768**, (or whatever value you got from the previous question)

> **What is a cast list**
>
> The cast list is the names of the actors who were in the movie.
>
> 将某个电影中的**全部演员**列出来

```sql
select name
from actor
join casting on actor.id=casting.actorid
where movieid=11768;
```



7. Obtain the cast list for the film 'Alien'

> Alien这个演员的cast list

```sql
select name
from actor
join casting on actor.id=casting.actorid
where movieid = (select movie.id
                 from movie
                 where title='Alien');
```



8. List the films in which 'Harrison Ford' has appeared

> 笔记：列出这个演员出现的所有电影。
>
> movie 和actor两个表是没有直接联系的，只能通过casting表的actorid字段来进行搭桥

```sql
select title
from movie
join casting on movie.id = casting.movieid
where actorid = (select id
                 from actor
                 where name='Harrison Frod');
```



9. List the films where 'Harrison Ford' has appeared - but not in the starring role.  [Note: the **ord** field of casting gives the position of the actor.  If ord=1 then this actor is in the starring role]

> 列出该演员出现但不是主演（ord=1）的电影

```sql
select title
from movie
join casting on movie.id=casting.movieid
where actorid = (select id
                 from actor
                 where name='Harrison Ford')
and ord != 1;
```



10. List the films together with the leading star for all 1962 films.

> 笔记：列出1962年的所有电影和主演名字

- 主演：ord-casting表
- 演员名字：name-actor表

```sql
select title, name
from movie
join casting on movie.id=casting.movieid
join actor on actor.id=casting.actorid
where yr=1962
and casting.ord = 1;
```

11. Which were the busiest years for 'Rock Hudson', show the year and the  number of movies he made each year for any year in which he made more  than 2 movies.

> 找出该演员的电影数目大于2的年份
>
> - 演员name
> - 年份yr
> - 电影movie

```sql
select yr, count(title)   -- 统计电影数目
from movie
join casting on movie.id = movieid  -- 联结顺序：movie---> casting---> actor
join actor on actorid = actor.id
where name = 'Rock Hudson'    -- 通过两个连接将movie 和 actor表进行联结
group by yr
having count(title) > 2;
```



12. List the film title and the leading actor for all of the films 'Julie Andrews' played in.

> 找出JA演员参加的电影和主演

```sql
select title, name   -- 找出电影和主演
from movie
join casting on (movieid=movie.id and ord=1)   -- 3. 找出主演
join actor on (actorid=actor.id)
where movie.id in (select movieid    -- 2.从1中选择出的全部演员中找出全部的电影movieid
                  from casting
                  where actorid in (select id   -- 1. 找出JA参加的电影的全部演员actorid
                                  from actor
                                  where name='Julie Andrews'))
```



**解题步骤**

1. 先找出JA参加的电影的全部演员actroid
2. 找出上步中的演员参演的电影movieid
3. 确定主演ord=1
4. 找出电影和主演

------



13. Obtain a list, in alphabetical order, of actors who've had at least 15 **starring** roles.

> 找出至少当过15次主角（ord，casting表）的演员名字name(actor)
>
> 按照名字的字母排序

```sql
select name
from actor
join casting on casting.actorid=actor.id
group by name
having sum(case ord when 1 then 1 else 0 end) >= 15  -- 当ord=1则计数1，sum求和
order by name;
```

14. List the films released in the year 1978 ordered by the number of actors in the cast, then by title.

> - 1978年发行的电影
> - 根据电影中的演员数量进行排名
> - 再根据title排名

```sql
select title, count(*) as actorcount
from movie
join casting on movie.id = casting.movieid
where yr=1978
group by title
order by actorcount desc, title
```



15. List all the people who have worked with 'Art Garfunkel'.

> 找出和AG合作的全部演员

```sql
select name
from actor join casting
on casting.actorid=actor.id
where movieid in (select movieid     -- 2. 筛选合作演员的电影
                  from casting
                  where actorid= (select id  -- 1. 筛选和AG合作的演员id
                                  FROM actor
                                  WHERE name='Art Garfunkel'))
and name != 'Art Garfunkel';
```



> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gbdq9eod0lj316x0u0jtq.jpg)
