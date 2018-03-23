---
title: hexo换主题
date: 2017-10-05 18:30:09
tags:
---

## 主题合集

> [https://github.com/hexojs/hexo/wiki/Themes ](https://github.com/hexojs/hexo/wiki/Themes )

## 随便找一个主题，进入主题的 GitHub 首页，比如我找的是  [Jacman](https://github.com/wuchong/jacman.git)

## 复制它的 SSH 地址或 HTTPS 地址，假设地址为 https://github.com/wuchong/jacman.git

## 比如我现在在自己的hexo blog根目录  myBlog  
### 一 进入themes目录
``` bash
$ cd themes
```

### 二 克隆该风格到你本地的主题
``` bash
$ git clone https://github.com/wuchong/jacman.git
```
### 三 切换回项目目录 
``` bash
$ cd ..
```

### 四 将 _config.yml 的第 75 行改为 theme: jacman，保存

### 五 重新初始化你的博客
``` bash
$ hexo generate
```
### 六 部署
``` bash
$ hexo deploy
```
### 七 等一分钟，然后刷新你的博客页面
[jacman这个主题的Blog](https://sltrust.github.io/index.html)! 

