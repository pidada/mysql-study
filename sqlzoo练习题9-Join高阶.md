---
title: sqlzoo练习题9-join高阶
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-14 20:05:03
password:
summary:
copyright: true
tags: 
  - sqlzoo
  - join
categories:
  - MySQL
  - sqlzoo练习
---

> Select the code which shows players, their team and the amount of goals they scored against Greece(GRE). 

<!--MORE-->

```sql
-- 选择出和德国对抗的比赛中选手、球队名称和总得分数
select player, teamid, count(*)
from game 
join goal on matchid = id
where (team1='GRE' or team2='GRE')
and teamid != 'GRE'
group by player, teamid
```



> Select the code which shows players, their team and the amount of goals they scored against Greece(GRE). 

```sql
-- 选择和POL对抗、在national 国际体育馆的比赛中的球员、球队名称

select distinct player, teamid   -- distinct表示去重
from game join on matchid=id
where stadium = 'National Stadium, Warsaw'
and (team1 = 'POL' or team2='POL')
and teamid != 'POL'
```



> Select the code which shows the player, their team and the time they scored, for players who have played in Stadion Miejski (Wroclaw) but not against Italy(ITA). 

```sql
-- 选择出在该体育馆，但不是和ITA对抗的比赛中，球员、球队和比赛时间信息
select distinct player, teamid, gtime
from game join on matchid = id
where stadium = 'Stadion Miejski (Wroclaw)'
and (( teamid = team2 AND team1 != 'ITA') 
     OR ( teamid = team1 AND team2 != 'ITA')
    )
```



----

```sql
SELECT teamname, COUNT(*)
FROM eteam JOIN goal ON teamid = id
GROUP BY teamname
HAVING COUNT(*) < 3
```

