---
title: sqlzoo练习11-join小测
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-22 21:34:02
password:
summary:
tags:
  - sql
  - sqlzoo
categories:
  - MySQL
  - sqlzoo练习
---


### Sqlzoo练习11-join quiz

`Join opetation`指的是不同的表之间通过某个**相同的字段进行关联**，从而进行查询操作。本文是对`Join`操作的小测

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gb3htlbln5j31gr0u0dp3.jpg)

<!--MORE-->

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gb5mzum66wj30u00u00ww.jpg)

### 练习

1. You want to find the stadium where player 'Dimitris Salpingidis' scored. Select the JOIN condition to use:

> 找到DS球员得分的体育场

```sql
select stadium
from game
join goal on (id=matchid)
where player='Dimitris Salpingidis';
```

2.  You JOIN the tables **goal** and **eteam** in an SQL statement. Indicate the list of column names that may be used in the SELECT line:

> 将goal和eteam表使用在SQL语句中，使用的字段可能有

```sql
matchid, teamid, player, gtime, id, teamname, coach
```

3. Select the code which shows players, their team and the amount of goals they scored against Greece(GRE).

> 找出和德国对抗中进球的数量

```sql
select player, teamid, count(*)
from game
join goal on matchid=id
where (team1 = 'GRE' or team2 = 'GRE')   -- 两只球队中选择一只
and teamid != 'GRE'  -- 另一只球队不是GRE
group by player, teamid;   -- 分组显示
```

4. Select the result that would be obtained from this code

```sql
select distinct teamid, mdate   -- 结果中有两个字段
from goal
join game on (matchid=id)
where mdate='9 June 2012';   -- 指定特殊时间
```

![](https://tva1.sinaimg.cn/large/006tNbRwgy1gb5naq4p6rj30ba04y74f.jpg)

5.  Select the code which would show the player and their team for those  who have scored against Poland(POL) in National Stadium, Warsaw.

> 选择出和POL在该体育场中得分的player、team

```sql
select distinct player, teamid
from game join goal on matchid = id
where stadium = ' National Stadium, Warsaw'  -- 指定球场
and (team1 = 'POL' or team2 = 'POL')  -- 两支球队中有一只是波兰
and teamid != 'POL';  -- 另一只不是波兰
```

6.  Select the code which shows the player, their team and the time they  scored, for players who have played in Stadion Miejski (Wroclaw) but not against Italy(ITA).

> 找出在这个体育场中不是和ITA对抗的球队player、teamid、gtime

```sql
select distinct player, teamid, gtime
from game join goal on matchid = id
where stadium = 'Stadion Miejski (Wroclaw)'
and ((teamid = team2 and team1 != 'ITA') or (teamid = team1 and team2 != 'ITA'))   -- 只需要其中一个球队不是ITA，另外两个是一样的即可
```

7. Select the result that would be obtained from this code

```sql
select teamname, count(*)
from eteam
join goal on
teamid=id
group by teamname   -- 分组
having count(*) < 3;  -- 过滤行
```



> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gb5nqxy140j30di07oaad.jpg)

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gb3htlbln5j31gr0u0dp3.jpg)
