---
title: React入门011之React开发环境搭建
date: 2019-01-26 15:41:05
tags: React入门
---

### 环境搭建

1. 环境搭建
    - Node 8 以上（稳定版）
    - Npm 6 以上（不需要单独安装因为 Node 自带 npm）
    - 编辑器 VSCode
   - 终端 Git Bash（Windows 用户）、iTerm 2（macOS 用户）
   - 版本管理 Git + GitHub
   - JS 版本：ES 6
   - CSS 版本：兼容 IE 12+、Chrome、Firefox（CSS 3 往上走）
2. Hello World
3. 部署 React 应用到 GitHub

> 文档

- https://reactjs.org/

### 安装本地 react 命令行环境

```
# 安装
npm i -g create-react-app

# 新建目录 
cd ~/Desktop/
create-react-app my-react-app

# 依赖安装成功后显示如下信息
Success! Created my-react-app at /Users/huangjiaxi/Desktop/my-react-app
Inside that directory, you can run several commands:

  yarn start
    Starts the development server.

  yarn build
    Bundles the app into static files for production.

  yarn test
    Starts the test runner.

  yarn eject
    Removes this tool and copies build dependencies, configuration files
    and scripts into the app directory. If you do this, you can’t go back!

We suggest that you begin by typing:

  cd my-react-app
  yarn start

# 一般我们只用如下三个命令
- yarn start
- yarn build
- yarn test

# 不建议用 yarn eject 尤其是新人


--------------------------
# 进入你的项目目录 并启动项目
cd my-react-app
yarn start

# 此时自动打开了一个3000端口的react项目
```

### 目录讲解

```
--|build  打包后的文件
--|public 万年不改的目录
--|src
----|App.css
----|App.js
----|index.css
----|index.js
----|registerServiceWorker.js
--|package.json
--|yarn.lock
```

index.js

```
import React from 'react';
import ReactDOM from 'react-dom';
// 你学会了 引入样式
import './index.css';
/*
为什么叫app，因为 index.js叫做入口 入口是把整个程序启动起来的
所有逻辑卸载 App 里
*/
import App from './App';
import * as serviceWorker from './serviceWorker';

ReactDOM.render(<App />, document.getElementById('root'));


// serviceWorker.unregister();
// 这个 serviceWorker 你不知道干啥的，你可以试着 删掉来看看
// 删除后依然可以运行 
```

> ### 怎么使用js?

aa.js

```
function sayHello(){
    console.log('hello');
}

export default sayHello
```

index.js

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

import sayHello from './aa';

sayHello(); // 打印 hello

ReactDOM.render(<App />, document.getElementById('root'));

```

> 这样你就知道如何引入 css 和 js 了

> ### index.html是什么

- 基本就是万年不变

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <!-- 网站图标 -->
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico" />
    <!-- 手机端适配 -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no"/>
    <meta name="theme-color" content="#000000" />
    <!-- 安卓应用相关 -->
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <title>React App</title>
  </head>
  <body>
    <!-- 浏览器不支持js -->
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
  </body>
</html>
```

App.js

```
import React, { Component } from 'react';
// app 里 引入了一个图片 svg 而且还能当作对象表示
import logo from './logo.svg';
// 引入样式
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <p>
            Edit <code>src/App.js</code> and save to reload.
          </p>
          <a
            className="App-link"
            href="https://reactjs.org"
            target="_blank"
            rel="noopener noreferrer"
          >
            Learn React
          </a>
        </header>
      </div>
    );
  }
}

export default App;
```

- 此时 App里的内容是不是很熟悉 JSX啊
- 注意 JSX 使用的是 xml语法而不是 html语法
- 注意 JSX 使用的是 xml语法而不是 html语法
- 注意 JSX 使用的是 xml语法而不是 html语法


### 部署到github上

```
cd my-react-app

# 初始化git
git init 

# 提交文件 到暂存区
git add .

# 所有代码提交到代码库
git commit -m '第一次提交'

# 打开你的github 新建仓库 
react-demo-2019-1-26

# 选择上传本地仓库
git remote add origin git@github.com:slTrust/react-demo-2019-1-26.git
git push -u origin master

# 注意 千万别用 https 坑死你 用SSH
```

> ### 如何让别人预览

```
# 第一步 cd my-react-app 进入你的应用目录
# 打包你的应用
yarn build 

/*
Creating an optimized production build...
Compiled successfully.

File sizes after gzip:

  34.71 KB  build/static/js/1.fa92c112.chunk.js
  763 B     build/static/js/runtime~main.229c360f.js
  581 B     build/static/js/main.419d3410.chunk.js
  511 B     build/static/css/main.d12a5979.chunk.css

//这个项目 默认认为你是在服务器的根目录
The project was built assuming it is hosted at the server root.
// 你可以控制这个东西 使用 homepage 参数 在 package.json.
You can control this with the homepage field in your package.json.
// 比如
For example, add this to build it for GitHub Pages:

  "homepage" : "http://myname.github.io/myapp",

The build folder is ready to be deployed.
You may serve it with a static server:

  yarn global add serve
  serve -s build

Find out more about deployment here:

  http://bit.ly/CRA-deploy

✨  Done in 7.99s.
*/


# 由于 .gitignore 忽略了 build 目录 所以我们要修改它 移除 /build
git add .
git commit -m '第一次 build'
git push


# 在你的远程仓库  点击 settings
# GitHub Pages 下 Source 选择 master branch ==> save 
# 此时给你个地址  千万别点 因为是 404
# 因为它不是给你点的  是给你复制点 如  我的是 https://sltrust.github.io/react-demo-2019-1-26/

# 然后找刚才的build 目录 如我的是：
https://sltrust.github.io/react-demo-2019-1-26/build/index.html

# 但是为什么一片空白
# 看 network 发现资源 404 
# 因为路径是 https://sltrust.github.io/static/css/main.d12a5979.chunk.css
# 而你没有 static 这个路径


# 回到刚才 build之后提示的  homepage 
# 去 package.json 里
# 添加  "homepage" : "http://myname.github.io/myapp",
# 修改为你的 github
https://sltrust.github.io/react-demo-2019-1-26/build

# 重新 build
yarn build

# 重新提交
git add .
git commit -m '第二次 build'
git push

# 重新打开网址  清除缓存
https://sltrust.github.io/react-demo-2019-1-26/build/index.html

# 如果是高手就设置一下预览链接
仓库目录  Code下  点击那个 edit 按钮
这样别人进入你的仓库就能看到预览了
```
