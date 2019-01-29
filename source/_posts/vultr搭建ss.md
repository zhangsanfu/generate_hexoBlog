---
title: vultr搭建ss
date: 2019-01-29 13:53:19
tags: vultr
---

### 一、挑选vps服务商

- vultr:支持支付宝微信付款、操作简单
- bandwagon:俗称"搬瓦工"，支持支付宝付款，价格便宜，套餐种类多，性价比高。
套餐种类:KVM架构方案包括 CN2 GT线路、CN2 GIA 线路、香港机房等。
地址:[搬瓦工中文网](https://www.bwgblog.org/)

### 二、注册vultr账号

#### 1.创建vultr账户

注册地址：[Vultr The Infrastructure Cloud™](https://www.vultr.com/?ref=7725423)

Vultr优惠注册链接：[https://www.vultrblog.com/](https://www.vultrblog.com/)

进入首页后填写邮箱地址，密码(需要数字大小写字母且十位以上)，然后点击create account按钮

![](https://raw.githubusercontent.com/slTrust/note/master/vultr/vultr001.png)

然后验证你的邮箱地址，如果没收到验证邮件的话去垃圾箱翻找一下。

#### 2.充值

进入我的账户，选择billing，然后选择alipay或者wechat，选择金额并发起付款，最少支付10$

![](https://raw.githubusercontent.com/slTrust/note/master/vultr/vultr002.png)

#### 3.搭建服务

> 1.首先选择机房地址，我选择的是东京，这个以后还可以更改

![](https://raw.githubusercontent.com/slTrust/note/master/vultr/vultr003.png)

> 2.选择系统，推荐选用CentOS

![](https://raw.githubusercontent.com/slTrust/note/master/vultr/vultr004.png)

> 3.选择套餐，推荐选用3.5$每月这款套餐，2.5$每月的只支持ipv6，可能连接不上

![](https://raw.githubusercontent.com/slTrust/note/master/vultr/vultr005.png)

> 4.最后这几个选项不用动，点击deploy now即可

![](https://raw.githubusercontent.com/slTrust/note/master/vultr/vultr006.png)

> 5.等待服务器状态变为running，点击服务器名

![](https://raw.githubusercontent.com/slTrust/note/master/vultr/vultr007.png)

> 6.跳转到以下页面后，查看服务器ip地址，用户名默认为root，记住密码

![](https://raw.githubusercontent.com/slTrust/note/master/vultr/vultr008.png)

> 完成以上步骤，服务就搭建完毕了，记住ip地址以及密码，接下来进行ssh连接

#### 4.使用ssh终端连接服务器

这里我使用的是mac的终端进行ssh连接

> 1.首先进行ssh登录，将用户名和ip地址替换为你的服务器的用户名与ip地址

```
ssh root@ip
此时如果成功会让你输入密码
复制就好
```

当出现[root@vultr ~]#时说明已经登录VPS成功，可进行以下步骤。

> 2.分别执行以下三个命令,安装ss

```
# 我选择进入一个本地目录,这样下载的东西更加可控
cd  /usr/local/src
mkdir shadowsocks
cd shadowsocks


# 下载命令
wget --no-check-certificate  https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks.sh

# 可执行权限
chmod +x shadowsocks.sh

# 这个过程最后会让你输入  port 和 password
./shadowsocks.sh 2>&1 | tee shadowsocks.log
```

此时你只有一个账号，就是你刚刚输入的端口号和密码

#### 5.配置多用户

有时候想给小伙伴一起分享这个ss，但又不想一起用一个号，那么我们最好配置多个用户

> 1.在登录的状态下(登录服务器的前提下)，输入以下命令，查看配置文件

```
cat  /etc/shadowsocks.json
```
默认配置

```
{
    "server":"0.0.0.0",
    "server_port":443,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"aabbcc",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open":false
}
```

> 2.修改默认配置

我就默认你是mac了,如果你是 window请安装gitbash

```
vi /etc/shadowsocks.json
```

点击i键进行编辑

```
{
    "server":"0.0.0.0",
    "local_address":"127.0.0.1",
    "local_port":1080,
    "port_password":{
         "9001":"pwd123",
         "9002":"pwd123",
         "9003":"pwd123",
         "9004":"pwd123"                    
    },
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

编辑完毕后，点击esc键，再输入:wq保存

> 3.开放端口

```
# 查看已开放端口
# 此时可能会导致你退出远程服务器
 firewall-cmd --list-ports

# 开启端口 以开启9001端口为例
# 就是shadowsocks.json里 你设置的账号端口
# 如果是多账号就执行多次(因为多个端口)
 firewall-cmd --zone=public --add-port=9001/tcp --permanent

# 重启防火墙
 firewall-cmd --reload
 
 
# 上述命令很有可能会导致你退出远程服务器
# 如果退出后，你远程链接都受限了，那就请 restart 你的服务器
```

在这里将新建的端口全部打开，如果不执行这部操作，很可能连接不上

> 4.重启ss

```
ssserver -c/etc/shadowsocks.json -d restart 
```

重启成功后，就可以将地址与密码分享给你的小伙伴咯。

### 6.使用BBR加速

```
wget –no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh
chmod +x bbr.sh
./bbr.sh
```

至此 Vultr（VPS）搭建SS教程已经结束，望大家都能自由上网。


### 7.如何测试是否成功了

- 我的是mac 安装好 ShadowsocksX-NG,客户端
- 服务器设置点击 +
    ```
    地址：你的服务器ip:你刚才添加的端口 如 9001
    加密算法：在你的 shadowsocks.json 里写着
    密码：就是刚才设置的如  pws123
    ```
- 点击确定
- 打开 [https://www.google.cn/](https://www.google.cn/)


> 如果是chrome 推荐你安装一个插件

- Proxy SwitchyOmega 能帮你自动切换(是否fq)

#### 分享不易

如果你打算买 vultr 的服务器

- [邀请码链接](https://www.vultr.com/?ref=7725423)

> 参考链接

如果它们还没被和谐

- [https://testerhome.com/topics/17112?locale=en](https://testerhome.com/topics/17112?locale=en)
- [https://my.oschina.net/u/3953544/blog/1935044](https://my.oschina.net/u/3953544/blog/1935044)







![](https://raw.githubusercontent.com/slTrust/note/master/vultr/vultr001.png)












