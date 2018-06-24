---
title: Python001基础
date: 2018-06-16 13:27:34
tags: python
---

### 注释

```
# 单行注释
# 我是注释
# 我是注释
# 我是注释

'''文档注释'''

'''
dd
dd  多行注释
dd
'''
```

### 变量声明

```
# 与js类似 动态类型具体有那些自行百度

注意布尔值为 True / False
```

### 基本类型转换方法

```
a = 1;
str(a)  '1'
b = '2'
int(b)  2
```

### 格式化输出

```
# 格式化输出

# \n换行
# \t tab制表


name = "hjx"
age = 18
height = 199
# print(name+age) 报错  因为 name 和 age类型不一样 相加报错
# print(name+str(age))  要把age 转换为字符串

print("========================")
print("我叫%s"%name)
print("我叫%s,今年%d岁,身高%dcm"%(name,age,height))
print("========================")
# 检查类型
print(type(name))
print(type(age))
print("========================")
# 输出字符
char1 = 65
char2 = 98

print("%c"%char1)
print("%c"%char2)


print("========================")

# 帮助文档

help(print)
```

### 运算符

```
+ 懒得说
- 懒得说
* 懒得说
/ 懒得说
// 取整除  商

11/2     5
11.0/2   5.5

11//2    5
2**2  4
3**3  27
a = 100
b = 111
交换a b的值
a,b = b,a
```

### 条件判断if

多重嵌套if使用 tab缩进

```
if a >10 and a <20 :
    if a == 15 :
        print("满足条件")
```

### 或且非

- and
- or 
- not

猜拳游戏

```
import random
com = random.randint(0,2)
user = int(input("猜拳：0剪刀 1石头 2布"))

if (com==0 and user ==1) or (com==1 and user ==2) or (com==2 and user ==0):
    print('算你厉害')
elif com == user:
    print('平')
else:
    print("输了")
```

### while循环

```
i = 1
while i<=9:
    j = 1
    while j<=i:
        print("%d*%d=%d"%(j,i,i*j),end="")
        j+=1
    print("")
    i+=1
```

### for 循环

```
name = 'abcdef'
for temp in name
    print(temp)
```

### 下标

```
name = 'abcdef'
name[0]   'a'
name[1]   'b'
name[2]   'c'
name[3]   'd'
name[4]   'e'
name[5]   'f'
```

### 切片

```
name = 'abcdef'

name[0]  'a'

#注意切片来了 ,口诀包头不包尾

name[0:4] 'abcd'
name[1:4] 'bcd'

#超过字符串长度咋办？
name[0:70] 'abcdef'
```

> 获取字符长度之len

```
#js里  'abc'.length = 3 
#python里 
name = 'abcdef'
# 获取字符串长度
len(name)  # 6
```

> 想要切片返回当前字符串的内容

```
name = 'fasfasdfl;kfdl;askfl;daskl;fkasl;'
length = len(name)
name[0:length]

#简洁写法 都可以获取整个字符串
name[0:]
name[:]
name[::]

# name[]报错
```

#### 切片之炮打隔一子

```
name = 'ababababab'
name[::2]  'aaaaa'
```
```
name[起始位置:结束位置:步长]
```

#### 切片之反序

```
name = '123456789'
name[::-1]  '987654321'
```

#### 切片其他用法
```
name = 'abcdef'
name[-1] 'f'
name[-2] 'e'

name[0:-1]  'abcde'

name[0:0] ''
```