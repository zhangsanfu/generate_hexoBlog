---
title: Py008-11-02部署实例之安装python
date: 2019-01-07 21:39:58
tags: M08
---

### 安装python

```
# 远程登录服务器后 

cd /usr/local/src

# python3.7.1
wget https://www.python.org/ftp/python/3.7.1/Python-3.7.1.tar.xz
# 解压
tar xf Python-3.7.1.tar.xz
# 进入解压文件目录
cd Python-3.7.1

# 安装依赖
yum -y install gcc-* openssl-* libffi-devel  sqlite-devel

# configure
./configure --enable-optimizations --with-openssl=/usr/bin/openssl

# make
make -j4

make install

# 默认安装路径为
/usr/local/lib/python3.7

# 验证python
python3 
```

#### 安装pip3

```
pip3 install --upgrade pip
# 此时会警告一个 ssl问题
# pip is configured with locations that require TLS/SSL, however the
# ssl module in Python is not available.

# 解决方案
# 去python源码目录
vi Modules/Setup 
# 把下边这段话的#去掉 211 SSL=/usr/local/ssl
212 _ssl _ssl.c \
213 -DUSE_SSL -I$(SSL)/include -I$(SSL)/include/openssl \
214         -L$(SSL)/lib -lssl -lcrypto

# 再次make
make -j4
make install

# 再次升级pip
pip3 install --upgrade pip
```

> pip安装软件

```
# 安装
pip3 install ipython
# 卸载
pip3 uninstall ipython

```

> #### 安装python虚拟环境

- virtualenv 是⼀一个创建隔绝的Python环境的⼯工具。virtualenv创建⼀一个包含所有必要 的可执⾏行行⽂文件的⽂文件夹，⽤用来使⽤用Python⼯工程所需的包。

```
pip3 install virtualenv

#  virtualenv 使用
# 当前目录 创建一个基于python3的虚拟环境 web
virtualenv -p python3 web
# 如何生效这个 环境呢
source web/bin/activate

# 此时如果你想在 web环境里安装 django
pip3 install django
# 创建一个django项目
django-admin.py startproject myweb
# 进入目录
cd myweb
# 启动项目
python3 manage.py runserver localhost:8080

# 默认你是访问不到的 要设置 allowhost
vi myweb/settings.py
# 修改如下
ALLOWED_HOSTS = ['*']
# 重新启动django
python3 manage.py runserver localhost:8080
```

> 退出  virtualenv 虚拟环境

```
deactivate
```