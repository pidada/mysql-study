---
title: MySQL学习6_DCL
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-03 09:10:53
password:
summary:
copyright: true
tags:
  - 数据库
  - MySQL
  - 数据控制语言
categories:
  - MySQL
  - 基础
---

### DCL

`data control language，DCL`，数据控制语言，主要是对数据库中的登录和用户的权限进行控制的语言，包含

- 用户登录`MySQL`数据库
- 修改用户密码及忘记密码如何解决
- 创建普通用户及授权
- 撤销权限`revoke`
- 查看权限及删除用户

![](https://s2.ax1x.com/2019/10/03/uwrNmd.jpg)

<!--MORE-->

### 全部命令

```sql
1. 查看数据库中的用户及信息
mysql -uroot -p
show databases;
use mysql;
show tables;
select user, host, passord from user;    # 所有的用户都在user表中

2. 创建新用户、授权、撤销权限和删除
-- 创建
create user "test"@"192.168.2.10" identified by"password";    # 指定用户test、ip和密码password
flush privileges;  # 刷新权限
mysql -utest -h192.168.2.10 -p  # 用test用户登录

-- 授权
grant select, insert, delete on shop.* to "test"@"192.168.2.10";   # shop 是数据库，test是数据库中的表
flush privileges;  # 刷新权限
systemctl restart mysql;  # 重启mysql

-- 创建用户的同时进行授权
grant select, insert, delete on shop.* to "test"@"192.168.2.10" identified  by"password";

-- 查看权限
show grants for "test"@"192.168.2.10"\G   # \G参数是为了输出好看

-- 撤销权限
revoke delete on shop.* to "test"@"192.168.2.10";   # 撤销shop数据库中test用户的delete权限
flush privileges;  # 刷新权限
systemctl restart mysql;  # 重启mysql

-- 删除用户
drop user "test"@"192.168.2.10";

-- 谨慎操作
grant all privileges on *.* to "test"@"192.168.2.10"  # 将所有的权限给所有数据库


3. 修改用户密码
-- 已知用户原密码，能够进行登录
mysql -uroot -p
show databases;
use mysql;
show tables;
select user, host, password from user;    # 所有的用户都在user表中
update user set password=PASSWORD("123456admin") where user="test";  # 将test用户的密码改成123456admin
flush privileges;

-- 忘记原来的密码：借助跳跃权限表，重启守护进程
mysql skip-grant-tables  # 跳跃权限表
mysql  # 重新进入mysql
show databases;use mysql;show tables;
select user, host, password from user;    # 所有的用户都在user表中
update user set password=PASSWORD("123456admin") where user="test";  # 将test用户的密码改成123456admin
flush privileges;

4. 查看mysql服务
-- window
直接去任务管理器
-- linux
netstat -an   # 找到3306端口
```



### 关于`root`账户

#### 默认登录

默认情况下，`MySQL`数据库是指允许`root`账户登录并且在本机上登录的。

- `-uroot`表示`root`账户
- `-p`表示需要密码
- 没有`-h`表示默认是本机`localhost`或者`127.0.0.1`登录

![](https://s2.ax1x.com/2019/10/03/uwJGhd.png)

#### 登录查看账户

`MySQL`数据库的服务端口号是`3306`，通过在`mysql`数据库的`user`表中查看登录数据库用户信息：

```sql
mysql> show databases;  # 查看所有的数据库
mysql> use mysql;   # 选择mysql数据库
mysql> show tables;   # 查表数据库中的所有表
mysql> select user, host from user;   # 查看这个表中的user和host信息
```

![](https://s2.ax1x.com/2019/10/03/uwJY9A.png)

#### 删除用户

需要注意的是删除了某个用户之后必须进行权限的刷新：

```sql
mysql> delete from user where host="%";   # 删除host为%的用户
Query OK, 1 row affected (0.01 sec)

mysql> flush privileges;  # 刷新权限
Query OK, 0 rows affected (0.00 sec)
```

**注意：当在实际的开发项目中，项目和数据库服务器不再同一个地方，可以指定ip连接进行访问。**

```sql
mysql> update user set host="192.168.1.10" where user="root";
Query OK, 1 row affected (0.02 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select user,host from user;

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

![](https://s2.ax1x.com/2019/10/03/uwJdnf.png)

当退出`mysql`重新进入，需要指定`IP`地址，就是上面设置的`IP`：

![]()![image](https://s2.ax1x.com/2019/10/03/uwJDAg.png)
  

### 密码问题

#### 修改密码

同样需要进入`mysql`数据库的`user`表中

```sql
mysql>update user set password=PASSWORD("admin")  where user="root";   # 将root账户的密码改为admin

mysql> flush privileges;   # 更新操作
Query OK, 0 rows affected (0.00 sec)
```

#### 忘记密码

- 如果忘记了密码，需要进入配置文件中

```shell
ubuntu@peter:~$ vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

- 找到下图中的`skip-grant-tables`，将前面的#去掉，就是取消注释：取消权限认证，后台开启新的进程免密进入`MySQL`

  ![](https://s2.ax1x.com/2019/10/03/uwJ69s.png)

- 重启`MySQL`服务：`systemctl restart mysql`
- 通过`MySQL`直接进入：

![](https://s2.ax1x.com/2019/10/03/uwJfBT.md.png)

- 然后按照上面的步骤重新设置密码即可

```csharp
mysql> show databases;
mysql> use mysql;
mysql> select user, password  from user;
# 接下来重新设置密码即可
```

![](https://s2.ax1x.com/2019/10/03/uwJ7C9.md.png)

### 创建新用户及授权

#### 创建用户

- 进入`mysql`数据库后，创建一个`test`用户，并且刷新权限：

![](https://s2.ax1x.com/2019/10/03/uwJjHO.png)

- 尝试登陆，创建成功：

![](https://s2.ax1x.com/2019/10/03/uwY9Cd.png)



- %使用，匹配所有：
  **如果想所有的IP都可以通过test账户访问mysql，将host指定为%；危险操作，谨慎使用**

```bash
msyql>update user set host="% where user="test" ;     # 谨慎操作
```



#### 用户授权

- 授权
  将`select、insert、delete`等操作权限授权给新建的用户`test`

![](https://s2.ax1x.com/2019/10/03/uwYuCj.md.png)

- 测试授权是否成功

![](https://s2.ax1x.com/2019/10/03/uwYK8s.png)

- 进入`mysql`数据库中，选中`peter`这个数据库中，创建一个`test`表

![](https://s2.ax1x.com/2019/10/03/uwY3rV.png)

- `create`权限成功

![](https://s2.ax1x.com/2019/10/03/uwYJVU.png)

- `insert`和`select` 权限成功

![](https://s2.ax1x.com/2019/10/03/uwYd2R.png)



- 将test账户的所有权限给所有数据库，`谨慎操作`

```css
grant all privileges on *.* to "test"@"192.168.21.136";
```

### 撤销权限

撤销权限使用的是`revoke`；承接上面的栗子，撤销`test`用户的`select`权限：

- `revoke`：撤销
- `select`：即将撤销的权限
- `peter`：从哪个数据库中撤销
- "test"@"192.168.21.136"：用户及密码

```sql
revoke select on peter.* from "test"@"192.168.21.136";
```

![](https://s2.ax1x.com/2019/10/03/uwY6aD.png)

**撤销权限一定要通过root账户进入MySQL；否则出现权限不足的问题**

![](https://s2.ax1x.com/2019/10/03/uwYRGd.md.png)



### 查看权限和删除用户

#### 查看权限

```
show grants for 用户名@IP地址;
```


  ![](https://s2.ax1x.com/2019/10/03/uwYHIg.md.png)

#### 删除用户

```
drop user 用户名@IP地址;
```

![](https://s2.ax1x.com/2019/10/03/uwYzLV.png)

![](https://s2.ax1x.com/2019/10/03/uwtAzR.png)