---
title: React入门007之Hooks
date: 2019-01-22 21:17:32
tags: React入门
---

### Hooks

- 英文教程[there](https://reactjs.org/docs/hooks-intro.html) and [there](https://reactjs.org/docs/hooks-reference.html)

- 中午教程[这里](https://github.com/dt-fe/weekly/blob/master/79.%E7%B2%BE%E8%AF%BB%E3%80%8AReact%20Hooks%E3%80%8B.md)

React新的API最新支持Hooks版本为16.7,需要单独安装

> 那么开始

1. 在 [https://codesandbox.io/](https://codesandbox.io/)上新建一个react项目
2. 在Dependencies上能看到React的版本(hooks的支持要求16.7.0-alpha)
3. 修改package.json
    ```
     "dependencies": {
        "react": "16.7.0-alpha",
        "react-dom": "16.7.0-alpha",
        "react-scripts": "2.0.3"
    },

    //此时保存报错了
    // 在命令行查看react的所有版本
    npm info react versions
    // 查看最新版本
    npm info react versions

    //再次修改 package.json

    "dependencies": {
        "react": "16.7.0-alpha.2",
        "react-dom": "16.7.0-alpha.2",
        "react-scripts": "2.0.3"
    },
    ```
4. 开始写我们的react应用 index.js
    ```
    import React from "react";
    import ReactDOM from "react-dom";

    function App() {
        return <div className="App">hello</div>;
    }

    const rootElement = document.getElementById("root");
    ReactDOM.render(<App />, rootElement);
    ```

### useState使用

> 有状态的函数组件

16.7以前只有class组件有状态，16.7开始，函数是可以有状态的


index.js

```
import React from "react";
import ReactDOM from "react-dom";

// step01 引入 useState
import { useState } from "react";

function App() {
  // 这样定义肯定不行
  // let count = 0;
  // let add = function(){}

  // 正确定义状态
  const [count, setCount] = useState(0); //count 初始值是 0
  const add = () => {
    setCount(count + 1);
  };

  const minus = () => {
    setCount(count - 1);
  };

  return (
    <div>
      <div>{count}</div>
      <div>
        <button onClick={add}>+1</button>
        <button onClick={minus}>-1</button>
      </div>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

[代码](https://codesandbox.io/s/86j8zn7ll)

核心代码

```
const [ 
        count, // 值
        setCount // 更新函数
    ] = useState(0); 

// useState 函数会返回一个数组
// 数组第一项表示 count的值
// 数组第二项表示 count的更新函数
```

> #### 自从有了useState之后，就不需要一个class去维护状态

- 直接 useState(初始值)
- setCount(count+1)  /  setCount(count -1) 就可以改变状态

而如果用之前的class组件写法是这样的

```
// 用 class写组件
class App2 extends React.Component {
  constructor() {
    super();
    this.state = {
      count: 0
    };
  }
  add() {
    this.setState({
      count: this.state.count + 1
    });
  }
  minus() {
    this.setState({
      count: this.state.count - 1
    });
  }
  render() {
    return (
      <div>
        app2
        <div>{this.state.count}</div>
        <div>
          <button onClick={this.add.bind(this)}>+1</button>
          <button onClick={this.minus.bind(this)}>-1</button>
        </div>
      </div>
    );
  }
}
```

> 结论就是：

- 用了hooks 之后语法简介了很多
- 再也不用class的方式写组件了
- 注意要将react升级到16.7.0
- 同时这里可以很爽的给数据赋值，不用setState了

#### useState的更多例子

复杂对象如何修改值？

```
import React from "react";
import ReactDOM from "react-dom";

// step01 引入 useState
import { useState } from "react";

function App() {
  const [count, setCount] = useState(0); //count 初始值是 0
  const [user, setUser] = useState({ name: "hjx", age: 18 });

  const add = () => {
    setCount(count + 1);
  };
  const minus = () => {
    setCount(count - 1);
  };

  const addAge = () => {
    setUser({
      ...user,
      age: user.age + 1
    });
  };
  return (
    <div>
      <div>{count}</div>
      <div>
        <button onClick={add}>+1</button>
        <button onClick={minus}>-1</button>
        <hr />
        userInfo:姓名 {user.name} 年纪{user.age}
        <button onClick={addAge}>age++</button>
      </div>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

- [线上代码](https://codesandbox.io/s/km4n666z2r)

> user添加爱好 hobbies

```
import React from "react";
import ReactDOM from "react-dom";

// step01 引入 useState
import { useState } from "react";

function App() {
  const [count, setCount] = useState(0); //count 初始值是 0
  const [user, setUser] = useState({
    name: "hjx",
    age: 18,
    hobbies: ["火影", "编程", "王者"]
  });

  const add = () => {
    setCount(count + 1);
  };
  const minus = () => {
    setCount(count - 1);
  };

  const addAge = () => {
    setUser({
      ...user,
      age: user.age + 1
    });
  };
  const addHobbie = () => {
    let num = parseInt(Math.random() * 100);
    setUser({
      ...user,
      hobbies: [...user.hobbies, num]
    });
  };

  return (
    <div>
      <div>{count}</div>
      <div>
        <button onClick={add}>+1</button>
        <button onClick={minus}>-1</button>
        <hr />
        userInfo:姓名 {user.name} 年纪{user.age}
        <button onClick={addAge}>age++</button>
        <hr />
        爱好：{user.hobbies.join(",")}
        <button onClick={addHobbie}>Hobbie++</button>
      </div>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

- [在线代码](https://codesandbox.io/s/jn8rmnvo35)

> 删除 hobbies第二项怎么处理

```
import React from "react";
import ReactDOM from "react-dom";

// step01 引入 useState
import { useState } from "react";

function App() {
  const [count, setCount] = useState(0); //count 初始值是 0
  const [user, setUser] = useState({
    name: "hjx",
    age: 18,
    hobbies: ["火影", "编程", "王者"]
  });

  const add = () => {
    setCount(count + 1);
  };
  const minus = () => {
    setCount(count - 1);
  };

  const addAge = () => {
    setUser({
      ...user,
      age: user.age + 1
    });
  };
  const addHobbie = () => {
    let num = parseInt(Math.random() * 100);
    setUser({
      ...user,
      hobbies: [...user.hobbies, num]
    });
  };

  const removeHobbie = () => {
    user.hobbies.splice(1, 1);
    setUser({
      ...user,
      hobbies: [...user.hobbies]
    });
  };

  return (
    <div>
      <div>{count}</div>
      <div>
        <button onClick={add}>+1</button>
        <button onClick={minus}>-1</button>
        <hr />
        userInfo:姓名 {user.name} 年纪{user.age}
        <button onClick={addAge}>age++</button>
        <hr />
        爱好：{user.hobbies.join(",")}
        <button onClick={addHobbie}>Hobbie++</button>
        <button onClick={removeHobbie}>删除第二个Hobbie</button>
      </div>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

- [在线代码](https://codesandbox.io/s/rjzzpypmko)

> 以上就是hooks的基本使用

> #### 疑问？能不能将 const [count, setCount] = useState(0) 放在函数组件外

- 这是API没有告诉我们的，会报错
- 没有为什么，就是不准

> #### 疑问2 能不能修改  const [count, setCount] = useState(0) 名字

- 可以


```
const [x, y] = useState(0)
const add = () => {
    y(x + 1);
}
```

### useEffect使用

- [文档](https://reactjs.org/docs/hooks-effect.html)

```
//没有作用的函数
function fn(){}


// 有作用的函数
function fn2(){ console.log(1) }

// 有作用的函数
function fn3(a,b){ return a + b }


// fn2 和 fn3 的区别
// fn2会让人有疑问 哪里来的 console
// fn3 不会让人有  a,b是哪里来的感觉
```

> 如果你在函数里看到了 不知道从哪里来的东西  这个函数就是有副作用的函数

- 因为它依赖了[不知道从哪里来的东西](比如你把家里收拾的很干净，此时突然从窗户里飘来个塑料袋，你就会觉得这东西不好)

```
// 来看什么是副作用
function fn2(){ console.log(1) }

fn2() //打印 1

fn2() //打印 1

console.log = function(){ }

fn2() //什么都不打印

// 如果你依赖的东西不知道从哪里来的
// 如果这个东西突然有一天被人改了，你也不知道
// 这就是 有副作用的函数的问题

// 再看 fn3 无论你怎么修改 都是返回 a+b的结果 
// fn3就叫做——纯函数 (不依赖外部不知道哪里来的东西的函数)
```

> #### 什么情况下用 effect

在 index.html 里添加一个节点元素 id=box1

```
<div id="box1" style="border:1px solid red;"></div>
<div id="root"></div>
```

index.js

```
import React from "react";
import ReactDOM from "react-dom";

// step01 引入 useState
import { useState, useEffect } from "react";

function App() {
  const [count, setCount] = useState(0); //count 初始值是 0

  const add = () => {
    setCount(count + 1);
  };

  useEffect(() => {
    // 你不知道 box1是从哪里来的
    document.querySelector("#box1").innerText = count;
  });

  return (
    <div>
      <div>{count}</div>
      <div>
        <button onClick={add}>+1</button>
      </div>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);

```

- [线上代码](https://codesandbox.io/s/njy6zq7n0)



