---
title: MySQL学习14-游标
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-12 18:23:38
password:
summary:
copyright: true
tags:
  - MySQL
  - cursor
  - 数据库游标
categories:
  - MySQL
  - 进阶
---

### 游标

> `SQL`检索操作返回的是一行或者多行称为结果集的行。

有时候我们是需要在检索的结果中，前进或者后退一行或者多行，这个时候需要使用游标`cursor`。

> 游标是一个存在`DBMS`服务器上的数据库查询，**它不仅是一条SELECT语句**，而是被该语句检索出来的结果集。在存储了游标之后，应用程序可以根据V需要滚动或者浏览其中的数据。不同的`DBMS`支持不同的游标选项和特性：

- 能够标记游标为只读，使得数据能够被读取，但是不能更新和删除
- 能控制执行定向的操作（向前，向后，第一，最后，绝对位置，相对位置等）
- 标记某些列能够编辑，有些列不能被编辑
- 规定范围，使得游标对创建它的特定请求或者所有请求可访问

<!--MORE-->

-----

### 使用游标

#### 使用步骤

- `declare`：在使用游标之前，必须先进行声明
- `open：一旦声明了游标，就必须打开游标`
- 对于填有数据的游标，根据需要取出检索的各行
- `close`：在结束的时候，必须关闭游标；有的`DBMS`还需要释放游标



#### 创建、打开游标

- 使用`declare`语句创建游标

- 使用`open`语句进行打开

```sql
-- 创建游标
declare CustCursor cursor
for 
select * from Customers 
where cust_email is null;

-- 打开游标
open cursor CustCurosr;
```

#### 访问游标数据

访问游标数据使用的关键字是`fetch`，功能：

- 检索哪些行
- 从何处开始检索
- 将检索的结果置于何处

#### 闭游标

关闭游标的关键词是`close`。游标一旦关闭，如果不再次打开，将无法使用；第二次使用的时候，不需要声明，直接`open`即可

```sql
close Custcursor   
```

----

### Python操作游标

#### 连接数据库

**使用的是`pymysql`模块，需要先进行导入**

```bash
import pymysql
```

- host：主机名或者`IP`地址
- port：默认是`3306`
- user：用户名
- passwd：`user`账户登录`mysql`的密码
- db：创建的数据库
- charset：防止中文出错，编码格式设置为`charset="utf8"`

**使用模块的`connect()`方法进行连接**

```python
import pymysql
conn = pymysql.connect(host=“localhost”,  # 主机名
                       port=3306,  # 端口
                       user="root",  # 用户名
                       passwd="123456",   # 密码
                       db="bookdb",   # 数据库名字
                       charset="utf8")  # 指定字符集
```

#### 操作数据库

> `Python`建立了和数据库的连接，实际上就是建立了一个`pymysql.connect()`的`实例对象`，或者称之为`连接对象`。
>
> `Python`就是通过连接对象和数据库进行对话。`pymysql.connect()`实例对象的方法有：

- `commit`：提交数据
- `rollback`：如果有权限，取消当前的操作，否则会报错
- `cursor([cursorclass])`：返回连接的游标对象，执行`SQL`语句
- `close()`：关闭连接

#### **游标对象cur的操作方法**

| 名称      | 描述                                                         |
| --------- | ------------------------------------------------------------ |
| close     | 关闭游标                                                     |
| execute   | 执行一条SQL语句，可以带上参数； 执行之后需要conn.commit()，数据库中才会真正建立 |
| fetchone  | 返回一条语句                                                 |
| fetchall  | 返回所有的语句                                               |
| fetchmany | 返回many条语句                                               |
| nextset() | 移动到下一个结果                                             |

- 插入

```python
import pymysql    # 导入模块
conn = pymysql.connect(host=“localhost”,  # 建立python和数据库的连接
                       port=3306, 
                       user="root", 
                       passwd="123456", 
                       db="bookdb", 
                       charset="utf8")      
cur = conn.cursor()  # 建立游标对象cur
cur.execute("insert into users (username, passwd, email) values (%s,  %s, %s)", ("python", "123456", "python@gmail.com"))   
conn.commit()  # 需要再执行conn.commit()

# 同时执行插入多条信息：多条信息用元组的形式表示
cur.executemany("insert into users (username, passwd, email) values (%s,  %s, %s)", (("python", "123456", "python@gmail.com"), ("java", "456789", "java@gmail.com"), ("php", "123789", "php@gmail.com")))
conn.commit()  # 需要再执行conn.commit()
```

- 查询

```python
>>cur.execute("select * from users")
>>lines = cur.fetchall()   # 返回所有的查询结果
>>for line in lines:   # 遍历返回所有的结果
    print(line)
    
# 只想返回一条结果
>>cur.execute("select * from users where id=1")  # 查询id=1

>>cur.execute("select * from users")
>>cur.fetchone()   # 返回一条结果；游标会跟着移动，直到将所有的数据取完
>>cur.fetchone() 
>>cur.fetchone() 
```

- 游标移动`scroll`到指定位置
- 操作存储在内存中的对象时候游标会移动，可以指定游标到某个位置：

```python
>>cur.scroll(2)  # 相对于当前位置移动2个单位
>>cur.fetchone()  # 显示数据
>>cur.scroll(2, "absolute")   # 加上参数，实现“绝对移动”，到第三条
```

**绝对移动的数字不能是负数，相对移动可以是负数**

`Python`的连接对象的游标方法中提供一个参数，将读取到的数据保存成字典形式：

```python
>>cur = conn.cursor(pymysql.cursors.DictCursor)
>>cur.execute("select * from users")
>>conn.commit()  # 需要再执行conn.commit()
>>cur.fetchall()
```

**更新**

```python
>>cur.execute("update users set username=s% where id=2", ("mypython"))   # 更新第二条语句
>>cur.execute("select * from users where id=2")
>>conn.commit()  # 需要再执行conn.commit()
>>cur.fetchone()
```

