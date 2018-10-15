---
title: Py004-03-02pymysql增删改查
date: 2018-10-15 22:08:19
tags: M04
---

### CRUD操作

- 要使用execute来防止sql注入
- 增删改  必须commit才能生效

```
import pymysql

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
# 增删查
sql='insert into user values(null,%s,%s)'

# 插入一条
# rows=cursor.execute(sql,('bbb','123'))
# print(rows)  # 打印 1

# 插入多条
# rows=cursor.executemany(sql,[('axx','333'),('bxx','444')])
# print(rows)  # 打印 2

conn.commit()
# 关闭
cursor.close()
conn.close()
```

#### 查询

- fetchone查一条默认返回以元组的形式
- cursor = conn.cursor(pymysql.cursors.DictCursor) 这样返回字典格式的数据
- fetchmany(n) n代表取的条数
- fetchall() 取所有


```
import pymysql

# 建立链接
conn=pymysql.connect(
    host='localhost',
    port=3306,
    user='root',
    password='123456',
    db='mydb2',
    charset='utf8'
)


# 拿到游标 默认返回元祖的格式
cursor=conn.cursor()
# 返回字典的格式
# cursor = conn.cursor(pymysql.cursors.DictCursor)

# 查询
sql='select * from user;'
rows=cursor.execute(sql)
# 取得结果
res = cursor.fetchone()
res2 = cursor.fetchone()
res3 = cursor.fetchone()
print(res)
print(res2)
print(res3)

conn.commit()
# 关闭
cursor.close()
conn.close()
```

> 光标位置

- scroll(index,mode='absolute')
- scroll(index,mode='relative')

```
cursor.scroll(3,mode='absolute') # 相对绝对位置移动 从头数3个取
print(cursor.fetchone())
```

```
print(cursor.fetchone())
cursor.scroll(2,mode='relative') # 相对相对位置移动 从上一条取出记录数2个取
print(cursor.fetchone())
```

> 获取插入之前数据的id

- cursor.lastrowid

```
import pymysql

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
# 增删查
sql='insert into user values(null,%s,%s)'

# 插入多条
rows=cursor.executemany(sql,[('axx','333'),('bxx','444')])
print(cursor.lastrowid)  # 打印插入之前id的值

conn.commit()
# 关闭
cursor.close()
conn.close()
```

