---
title: P003_python基础数据类型
date: 2018-07-19 22:50:33
tags: fullstack
---

### 1. 数字int。

> 数字主要是用于计算用的，使用方法并不是很多，就记住一种就可以：

```
#bit_length() 当十进制用二进制表示时，最少使用的位数
v = 11
data = v.bit_length()
print(data)

'''
0000 0000   0  1
0000 0001   1  1
0000 0010   2  2
0000 0011   3  2
0000 0100   4  3
0000 0101   5  3
'''
```

### 2.布尔值bool

> 布尔值就两种：True，False。就是反应条件的正确与否。

- 真   1   True。
- 假   0   False。

> int ----> str
```
i = 1
s = str(i)
```

> str ---> int
```
s = '123'
i = int(s)

s2 = '123a'
i2 = int(s2) #报错
```

> int ----->bool  只要是0 ----》False  非0就是True
```
i = 3
b = bool(i)
print(b)
```
> bool----> int

```
#True   1
#False  0
'''
```
> ### ps:大神的高端写法

```
while True:
    pass
while 1:  # 效率高
    pass
```

> str --->bool
```
#s = "" -----> False
#非空字符串都是True
#s = "0" -----> True
# s
# if s:
#     print('你输入的为空，请重新输入')
# else:
#     pass
```

### 3.字符串str

#### 字符串的索引与切片。

> 索引即下标，就是字符串组成的元素从第一个开始，初始索引为0以此类推。

```
a = 'ABCDEFGHIJK'
print(a[0])
print(a[3])
print(a[5])
print(a[7])
```

切片就是通过索引（索引：索引：步长）截取字符串的一段，形成新的字符串（原则就是顾头不顾腚）。

```
a = 'ABCDEFGHIJK'
print(a[0:3])
print(a[2:5])
print(a[0:]) #默认到最后
print(a[0:-1]) #-1就是最后一个
print(a[0:5:2]) #加步长
print(a[5:0:-2]) #反向加步长
```

```
'''
s = 'ABCDLSESRF'
#索引
# s1 = s[0]
# print(s1)
# s2 = s[2]
# print(s2)
# s3 = s[-1]
# print(s3)
# s4 = s[-2]
# print(s4)
# #ABCD   切片 ：顾头不顾尾
# s5 = s[0:4]
# print(s5)
# s6 = s[0:-1]
# print(s6)
# s7 = s[:]
# s8 = s[0:]
# print(s7,s8)
# s9 = s[0:0]
s = 'ABCDLSESRF'  # s[首:尾:步长]
# s10 = s[0:5:2]
# print(s10)
s11 = s[4:0:-1]
print(s11)
s12 = s[3::-1]
print(s12)
s13 = s[3::-2]
print(s13)
s = 'ABCDLSESRF'
s14 = s[-1::-1]
print(s14)
s15 = s[::-1]
print(s15)
'''
```

#### 字符串常用方法

> 请参考我之前写的python文章 《Python002字符串》



