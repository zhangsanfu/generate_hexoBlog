---
title: React入门008之Router
date: 2019-01-23 20:57:45
tags: React入门
---

> 先来一个选项卡

- 打开 [https://codesandbox.io/](https://codesandbox.io/) 选择react
- 注意我用的react版本是16.7
    ```
    "dependencies": {
        "react": "16.7.0-alpha.0",
        "react-dom": "16.7.0-alpha.0",
        "react-scripts": "2.0.3"
    },
    ```

style.css

```
.box {
  border: 1px solid #ccc;
  text-align: center;
  padding: 100px 0;
  width: 90%;
  margin: 0 auto;
}
```

index.js

```
import React from "react";
import ReactDOM from "react-dom";
import { useState } from "react";
import "./styles.css";

function Login() {
  return <div className="box">登录</div>;
}

function Register() {
  return <div className="box">注册</div>;
}

function App() {
  let [ui, setUi] = useState("登录");
  let onClickLogin = () => setUi("登录");
  let onClickRegister = () => setUi("注册");
  return (
    <div className="App">
      <button onClick={onClickLogin}>login</button>
      <button onClick={onClickRegister}>register</button>
      <div>{ui === "登录" ? <Login /> : <Register />}</div>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

- [在线代码](https://codesandbox.io/s/y2y4qvl3jx)

> 需求来了，我现在想把链接分享给别人，如分享注册页面，结果链接打开还是登录

### 使用hash做路由

```
hash 就是锚点  就是url后面有个 '#'+path
```
- [在线代码(https://codesandbox.io/s/n41xmw192l)
- https://k3x1jy7o2o.codesandbox.io/#login
- https://k3x1jy7o2o.codesandbox.io/#register

```
import React from "react";
import ReactDOM from "react-dom";
import { useState } from "react";
import "./styles.css";

function Login() {
  return <div class="box">登录</div>;
}

function Register() {
  return <div class="box">注册</div>;
}

function App() {
  let hash = window.location.hash;
  // hash默认是带 '#'号的 
  let initUi = hash === '#register'?'注册':'登录';

  let [ui, setUi] = useState("登录");
  let onClickLogin = () => {
    setUi("登录");
    window.location.hash = 'login'
  };
  let onClickRegister = () => {
    setUi("注册");
    window.location.hash = 'register'
  };
  return (
    <div className="App">
      <button onClick={onClickLogin}>login</button>
      <button onClick={onClickRegister}>register</button>
      <div>{ui === "登录" ? <Login /> : <Register />}</div>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

> 以上我们完成了

- 用户根据 hash 值来进入对应页面
- 虽然url有点丑，先忍着吧！

### 除了 hash 还能用什么

- window.location.pathname

> location.pathname 的问题

- 修改路径会造成页面刷新
- 如果后台不进行url重定向(单页应用url指向index.html) 就会造成 404

### 不刷新修改页面路径的API——pushstate API

```
window.history.pushState(null,'','/login')
window.history.pushState(null,'','/register')
```

> 虽然这样改不造成页面刷新

但是

- 如果后台不进行url重写 就会报 404 如 https://www.baidu.com/login
- codesendbox不会404是因为 服务器配置好了。

> #### 三种方式

```
window.location.hash = // 让url变化同时不刷新页面
✅ 

window.location.pathname =  // 会刷新页面 ，基本不考虑
x 肯定不用这个

window.history.pushState(null,'',path) // 不刷新页面，修改路径 
? 这个是不确定的
    因为有两种情况
        1. 后端会将所有路径指向首页 就可以这样做
        2. 后端傻X 那你就放弃吧
        3. 既然后端傻X，那你就学node自己做后端控制url 抢他饭碗
```

> 什么叫url指向首页

```
你可以在codesendbox里 创建一个react项目 然后在浏览器的路径后随便修改
输入如下路径都进入首页

https://www.xxx.com/xxx
https://www.xxx.com/bbb
https://www.xxx.com/ccc
https://www.xxx.com/fuck
https://www.xxx.com/shit

```

> 最稳妥的方式是用 hash 100%没问题
> 最不靠谱的是用 pathname 因为会刷新页面
> 有个靠谱后端的情况下 用 pushState 或者你会nodeJs自己 控制url指向 index.html


### 用pushState实现路由

index.js

```
import React from "react";
import ReactDOM from "react-dom";
import { useState } from "react";
import "./styles.css";

function Login() {
  return <div className="box">登录</div>;
}

function Register() {
  return <div className="box">注册</div>;
}

function App() {
  let path = window.location.pathname;
  // hash默认是带 '#'号的
  let initUi = path === "/register" ? "注册" : "登录";
  console.log(initUi);
  let [ui, setUi] = useState(initUi);
  let onClickLogin = () => {
    setUi("登录");
    history.pushState(null, "", "login");
  };
  let onClickRegister = () => {
    setUi("注册");
    history.pushState(null, "", "register");
  };
  return (
    <div className="App">
      <button onClick={onClickLogin}>login</button>
      <button onClick={onClickRegister}>register</button>
      <div>{ui === "登录" ? <Login /> : <Register />}</div>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

- https://4qqoy05wo9.codesandbox.io/register
- https://4qqoy05wo9.codesandbox.io/login
- [在线代码](https://codesandbox.io/s/kwz7j1w7or)

> 分析：现在可以解决路由问题了

> #### 新的问题：无穷多个路由的时候怎么办

```
- login
- register
- index
- trade
- user   // 用户列表
- user/1 //  用户id为1的
- user/2 //  用户id为2的
- ...
- user/100  //用户id为100的

此时 

if else 明显不够用
```

> 使用库ReactRouter

### ReactRouter

- 强烈建议看英文文档
- 强烈建议看英文文档
- 强烈建议看英文文档

- [这个文档是中文的但是用的是老版本也就是过时的网页，各种问题](http://react-guide.github.io/react-router-cn/) 

- 你可以打开github 搜 router 选择 react的router [https://github.com/ReactTraining/react-router](https://github.com/ReactTraining/react-router)
- 或者 google 搜 react router 得到一个网址[https://github.com/ReactTraining/react-router](https://github.com/ReactTraining/react-router)

- [点击预览链接](https://reacttraining.com/react-router/)
- [选择web](https://reacttraining.com/react-router/web/guides/quick-start)

- 开始抄


初始化依赖(如果你用的create-react-app命令)

```
npm install -g create-react-app
create-react-app demo-app
cd demo-app

npm install react-router-dom
```

如果你用的 codesendbox.io 那就直接安装依赖 react-router-dom

index.js [在线代码](https://codesandbox.io/s/m781mqxooy)

```
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter as Router, Route, Link } from "react-router-dom";

const Index = () => <h2>Home</h2>;
const About = () => <h2>About</h2>;
const Users = () => <h2>Users</h2>;

function App() {
  return (
    <div className="App">
      <Router>
        <div>
          <nav>
            <ul>
              <li>
                <Link to="/">Home</Link>
              </li>
              <li>
                <Link to="/about/">About</Link>
              </li>
              <li>
                <Link to="/users/">Users</Link>
              </li>
            </ul>
          </nav>

          <Route path="/" exact component={Index} />
          <Route path="/about/" component={About} />
          <Route path="/users/" component={Users} />
        </div>
      </Router>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

唯一的坑在这里

```
import { BrowserRouter as Router, Route, Link } from "react-router-dom";
```

如果是你？ 你搜索后会选择去中文文档还是英文的？

```
中文——给你个例子——死活运行不了

英文——照着抄 (少走弯路)
```