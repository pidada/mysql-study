---
title: MySQL学习2_ubuntu18安装MySQL
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-01 13:18:58
password:
summary:
copyright: true
tags:
  - MySQL
  - 数据库
  - ubuntu18
categories:
  - MySQL
  - 基础
---

### 安装

#### 官网下载

- 进入`MySQL`数据库的[官网](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdownloads%2Fmysql%2F5.7.html%23downloads)。里面有各种版本，`MySQL5.5，MySQL5.6，MySQL5.7，MySQL8.0`。
- 其中前三者是应用最广泛的，`8.0`目前刚出来，属于起步阶段，问题还是很多；而且网上的资源比较少。

#### `MySQL5.7`社区版本

笔者安装`5.7`，找到对应的版本。

<!-- MORE -->

![](https://s2.ax1x.com/2019/10/01/uNhVHJ.md.png)

#### 选择安装版本

本次安装通过源码进行安装，并且选择`Linux`系统版本为`ubuntu18.04`，笔者最喜欢的版本。

![](https://s2.ax1x.com/2019/10/01/uNhcUs.png)

#### 下载 DEB Package

点击下载`DEB`安装包

![](https://s2.ax1x.com/2019/10/01/uNhW80.png)

![](https://s2.ax1x.com/2019/10/01/uNhf2V.md.png)

![](https://s2.ax1x.com/2019/10/01/uNhhvT.png)



#### 安装过程

进入到安装包所在的文件目录下，笔者是`Downloads`下：

```shell
sudo dpkg -i mysql-community-source_5.7.26-1ubuntu18.04_amd64.deb 
# 更新软件源
sudo apt-get upgrade
```

#### 安装客户端和环境

```shell
# 安装mysql服务
sudo apt-get install mysql-server
# 安装客户端
sudo apt install mysql-client
# 安装依赖
sudo apt install libmysqlclient-dev
# 检查状态
sudo netstat -tap | grep mysql
```

![](https://s2.ax1x.com/2019/10/01/uNhzrD.png)

### 设置`root`密码

`mysql5.7`安装完成后普通用户不能进`mysql`，原因：`root`的`plugin`被修改成了`auth_socket`，用密码登陆的`plugin`应该是`mysql_native_password`，直接用`root`权限登录就不用密码，修改`root`密码和登录验证方式：

```shell
$sudo su  # 进入root账户
# mysql   # 输入mysql，进入mysql服务  ；root账户下分隔符是#，普通用户是$

mysql> select user, plugin from mysql.user;
+------------------+-----------------------+
| user             | plugin                |
+------------------+-----------------------+
| root             | auth_socket           |
| mysql.session    | mysql_native_password |
| mysql.sys        | mysql_native_password |
| debian-sys-maint | mysql_native_password |
+------------------+-----------------------+
4 rows in set (0.00 sec)
mysql> update mysql.user set authentication_string=PASSWORD('renrenren'), plugin='mysql_native_password' where user='root';   # 修改root账户密码
mysql> flush privileges;  # 刷新权限
mysql> exit  # 退出msyql服务
Bye
# exit   # 退出root账户，进入普通账户（第一个星号不是注释）
$ sudo /etc/init.d/mysql restart  # 重启msyql服务
$ mysql -uroot -p   # 进入mysql服务中，接下来输入上面步骤中设置的密码
Enter password: （上面的密码）
```

![](https://s2.ax1x.com/2019/10/01/uN4FPI.md.png)

### 配置远程登录

默认的`MySQL`服务是只允许本地服务的，但是大部分时候需要远程服务，下面进行配置：

```bash
$sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf  # 进入配置文件，注释掉bind-address = 127.0.0.1
```

![](https://s2.ax1x.com/2019/10/01/uN4lin.png)

保存退出，进入mysql服务，执行授权命令：

```bash
$sudo -uroot -p
Enter password: （上面的密码）
mysql> grant all on *.* to root@'%' identified by 'renrenren' with grant option;
Query OK, 0 rows affected, 1 warning (0.00 sec)
mysql>flush privileges;
mysql> exit
Bye

$sudo /etc/init.d/mysql restart    # 重启mysql配置服务
```



### 卸载

#### 卸载`mysql`

```csharp
sudo apt-get autoremove --purge mysql-server
sudo apt-get autoremove --purge mysql-server-*
sudo apt-get autoremove --purge mysql-client
sudo apt-get autoremove --purge mysql-client-*
sudo apt-get remove mysql-common
```

#### 删除数据

```ruby
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P 
```

**弹出的窗口都要选择yes！**

#### 删除目录

```csharp
sudo rm -rf /etc/mysql
sudo rm -rf /var/lib/mysql
```

#### 清除残留

```undefined
sudo apt autoremove
sudo apt autoreclean
```



[MySQL解决普通用户登录等问题](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fgulang03%2Farticle%2Fdetails%2F82790821)
[Linux系统ubuntu18.04安装MySQL5.7](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2Fyellowgg%2Fp%2F9709670.html)