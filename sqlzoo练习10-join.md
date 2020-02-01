---
title: sqlzoo练习10-join
top: false
cover: false
toc: true
mathjax: true
copyright: true
date: 2020-01-21 00:31:16
password:
summary:
tags:
categories:
---

### sqlzoo练习10-Join opreation

`Join opetation`指的是不同的表之间通过某个**相同的字段进行关联**，从而进行查询操作。

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gb3htlbln5j31gr0u0dp3.jpg)

<!--MORE-->

通过下面的两张图中，可以得出3个结论

- game和goal两张表，是通过id和matchid来进行联结
- game和eteam两张表，是通过id字段进行联结
- goal和eteam两张表，通过matchid和id字段进行联结

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gb127vyfjqj30u00wbaeu.jpg)



### 练习

1. **show the** *matchid* **and** *player* **name for all goals scored by Germany. To identify German players, check for:** `teamid = 'GER'`

```sql
select matchid, player
from goal
where teamid='GER';
```

2. From the previous query you can see that Lars Bender's scored a goal in game 1012. Now we want to know what teams were playing in that match.

   Notice in the that the column `matchid` in the `goal` table corresponds to the `id` column in the `game` table. We can look up information about game 1012 by finding that row in the **game** table.

   **Show id, stadium, team1, team2 for just game 1012**

```sql
select distinct(id), stadium, team1, team2
from game
join goal on game.id=goal.matchid
where goal.matchid=1012;
```

3. **show the player, teamid, stadium and mdate for every German goal.**

> 找出每个德国队的球

```sql
select player, teamid, stadium, mdate
from game 
join goal on (id=matchid)
where goal.teamid='GER';
```

4. **Show the team1, team2 and player for every goal scored by a player called Mario** `player LIKE 'Mario%'`

> 找出一个名叫Mario的球员进的球

```sql
select team1, team2, player
from game
join goal on id=matchid
where player like 'Mario';
```

5. **Show** `player`**,** `teamid`**,** `coach`**,** `gtime` **for all goals scored in the first 10 minutes** `gtime<=10`

```sql
select player, teamid, coach, gtime
from goal
join eteam on teamid=id
where gtime <= 10;   -- 找出10分钟之内进的球
```

6. **List the the dates of the matches and the name of the team in which 'Fernando Santos' was the team1 coach.**

> 找出team1的较量是FS的球队情况

```sql
select mdate, teamname
from game
join eteam on team1=eteam.id
where coach='Fernando Santos';
```

7. **List the player for every goal scored in a game where the stadium was 'National Stadium, Warsaw'**

> 找出在National Stadium, Warsaw进球的球员

```sql
select player 
from goal
join game on id=matchid
where stadium='National Stadium, Warsaw';
```

8. **show the** **name** **of all players who scored a goal against Germany.**

> 找出和德国对抗的进球的球员信息

```sql
select distinct(player)
from goal
join game on id=matchid
where (team1='GER' or team2='GER')   -- 两个队中有一个是德国队
and teamid != 'GER'   -- 保证对手不是德国队
```

9. **Show** **teamname** **and the total number of goals scored.**

> 找出球队名称和总进球数

```sql
select teamname, count(player) as total_scoer   -- 进球总数通过球员总数来求得
from eteam
join goal on id=matchid
group by teamname;
```

10. **Show the stadium and the number of goals scored in each stadium.**

> 显示每个球场和每个球场中的总得分数

```sql
select stadium, count(stadium)
from game 
join goal on id=matchid
group by stadium;
```

11. **For every match involving 'POL', show the matchid, date and the number of goals scored.**

> 找出每场合POL相关的比较的情况

```sql
select matchid, mdate, count(teamid)    -- 进球总数通过球队数来统计
from game 
join goal on matchid=id
where (team1='POL' or team2='POL')
group by matchid, mdate
```

12. **For every match where 'GER' scored, show matchid, match date and the number of goals scored by 'GER'**

> 找出每场GER进球的比赛和GER进球总数

```sql
select matchid, mdate, count(teamid)   -- 统计进球数
from goal
join game on id=matchid 
where teamid='GER'
group by matchid, mdate;
```

13. **List every match with the goals scored by each team as shown. This will use "**[CASE WHEN](https://sqlzoo.net/wiki/CASE)**" which has not been explained in any previous exercises.**

Notice in the query given every goal is listed. If it was a team1 goal then a 1 appears in score1, otherwise there is a 0. You could SUM this column to get a count of the goals scored by team1. 

```sql
select mdate,
team1,sum(case when teamid=team1 then 1 else 0 end) scoer1,
team2,sum(case when teamid=team1 then 1 else 0 end) scoer2
from game
join goal on matchid=id
group by mdate, team1, team2
```

> ![](https://tva1.sinaimg.cn/large/006tNbRwgy1gb3htlbln5j31gr0u0dp3.jpg)
