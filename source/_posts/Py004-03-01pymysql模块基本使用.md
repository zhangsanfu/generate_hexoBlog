---
title: Py004-03-01pymysql模块基本使用
date: 2018-10-15 21:24:25
tags: M04
---

### pymysql

```
安装python连接数据库的模块pymysql
#pip3 install pymysql 
```

#### 初始工作

建库建表建数据

```
create database mydb1

create table user (
    id int primary key auto_increment,
    user varchar(10) not null,
    pwd varchar(10) not null
);

 insert into user values(null,'aaa','123'),(null,'bbb','111');
```

#### pymysql使用

```
import pymysql

user=input('user>>: ').strip()
pwd=input('password>>: ').strip()

# 建立链接
conn=pymysql.connect(
    host='localhost',
    port=3306,
    user='root',
    password='123456',
    db='mydb2',
    charset='utf8'
)


# 拿到游标
cursor=conn.cursor()

# 执行sql语句

sql='select * from user where user = "%s" and pwd="%s"' %(user,pwd)
print(sql)
rows=cursor.execute(sql)

cursor.close()
conn.close()

# 进行判断
if rows:
    print('登录成功')
else:
    print('登录失败')

```

#### sql注入

> 上述程序执行时你这样输入

你发现没输入密码也成功了，这就是sql注入

```
user>>: aaa" -- xxxx
password>>:回车
select * from user where user = "aaa" -- xxxx" and pwd=""
登录成功
```

sql注入的几种作案方式

```
select * from user where user = "aaa" -- xxxx" and pwd=""
sql语句中"--" 代表注释
"-- "之后就没有意义了
实际运行的是 select * from user where user = "aaa"

---------------------------------------------
user>>: xxx" or 1=1 -- hahaha
password>>:回车
select * from user where user = "xxx" or 1=1 -- hahaha" and pwd=""
登录成功

or 1=1  代表永远成立

```

#### 解决sql注入

- cursor.execute来防止注入
- execute来帮你拼接参数条件

```
# sql='select * from user where user = "%s" and pwd="%s"' %(user,pwd)
# print(sql)
# rows=cursor.execute(sql)

sql='select * from user where user = %s and pwd=%s'
print(sql)
rows=cursor.execute(sql,(user,pwd))
```