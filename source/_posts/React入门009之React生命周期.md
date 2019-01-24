---
title: React入门009之React生命周期
date: 2019-01-24 21:20:37
tags: React入门
---

### React生命周期

- [英文文档](https://reactjs.org/docs/state-and-lifecycle.html)
- [中文文档](https://react.docschina.org/docs/state-and-lifecycle.html)

![](https://raw.githubusercontent.com/slTrust/note/master/react/react-study009-01.png)

> 面试必考(但是平时用的不多)

> #### VanillaJS理解一个div的生命周期

```
<body>
    <div id="root"></div>
</body>
<script>
let app = document.getElementById('root');

// create 出现在js内存里
let div = document.createElement('div');
div.style.border = '1px solid red';

// mount 之前 
let state = 0;
div.innerHTML = `<p>${state}</p>
                <button>add</button>
                <button>die</button>
                `;

// mount 挂载
app.appendChild(div);



div.querySelector('button').onclick = ()=>{
    state += 1
    // update div
    div.querySelector('p').innerText = state;
}

div.querySelectorAll('button')[1].onclick = ()=>{
    // remove div 但是要移干净
    // 移除事件，移除引用
    div.querySelector('button').onclick = null
    div.querySelectorAll('button')[1].onclick = null
    div.remove()
    div = null
    // 此时才是真正的  destroy div
}
</script>
```

> 人有生/老/病/死
> div有 create / mount / update / destroy (生 挂 更 死)

### 此时看React版的生命周期(完整版)

[在线代码](https://codesandbox.io/s/n9lj1nplxp)

- constructor   创建
- componentWillMount 将要挂载
- render 填充/更新
- componentDidMount 挂载完毕
- componentWillUpdate 更新之前
- shouldComponentUpdate 要不要更新
- componentDidUpdate 更新之后
- componentWillUnmount 没死之前
- componentWillReceiveProps 父组件传递props了


```
import React from "react";
import ReactDOM from "react-dom";
import "./styles.css";

let div = document.createElement("div");
document.body.appendChild(div);
console.log = function(content) {
  div.innerHTML += `${content}<br>`;
};

class Baba extends React.Component {
  constructor() {
    super();
    this.state = {
      hasChild: true
    };
  }
  onClick() {
    this.setState({
      hasChild: false
    });
  }
  callSon() {
    this.setState({
      word: "你还好吧"
    });
  }
  render() {
    return (
      <div>
        我是你爸爸
        <button onClick={() => this.onClick()}>kill son</button>
        <button onClick={() => this.callSon()}>call son</button>
        {this.state.hasChild ? <App word={this.state.word} /> : null}
      </div>
    );
  }
}

class App extends React.Component {
  onClick() {
    console.log("用户点击了");
    this.setState({
      n: this.state.n + 1
    });
  }
  updateX() {
    this.setState({
      x: this.state.x + "!"
    });
  }
  constructor() {
    super();
    this.state = {
      n: 0,
      x: "不展示"
    };
  }
  componentWillMount() {
    console.log("将要 mount App");
  }
  render() {
    // update
    console.log("填充/更新 App 的内容");
    return (
      <div className="App">
        {this.state.n}
        <br />
        我爸说 {this.props.word}
        <br />
        <button onClick={() => this.onClick()}>+1</button>
        <button onClick={() => this.updateX()}>update x</button>
      </div>
    );
  }
  componentDidMount() {
    console.log("mount App 完毕");
  }
  componentWillUpdate() {
    console.log("update App 之前");
  }
  shouldComponentUpdate(nextProps, nextState) {
    console.log("要不要更新呢？");
    if (this.state.n === nextState.n) {
      return false;
    } else {
      return true;
    }
  }
  //update is render
  componentDidUpdate() {
    console.log("update App 之后");
  }
  componentWillUnmount() {
    console.log("App 快要死了，记得喂狗");
  }
  componentWillReceiveProps() {
    console.log("我爸说话了");
  }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<Baba />, rootElement);
```

### 我们在页面一般做什么？

1. ajax请求数据
2. 更新数据 setState
3. 事件监听 onClick
4. 初始化state

```
什么时候请求数据呢？ 理论上越早越好，任何周期都可以发请求
- 创建的时候  (推荐)
- 用户点击事件触发的时候 (推荐)
- 组件将要 mount 的时候
- mount之后


什么时候初始化呢？
- 可以在 constructor (推荐)
- 可以在 componentWillMount 里 (不推荐)
- render呢？ 可以 但是要在return之前 因为此时要用到 state 了


什么时候更新 state呢？
- 在 constructor 创建state之后 setState 不可以报错 Can't call setState on a component that is not yet mounted. 
- 不能在 render 里 setState() 这样就死循环了 因为 setState会触发 render
- 不能在 mount 之前 也不能在 render 里 setState
- 为什么在 componentWillUpdate 不能 因为setState会触发  将要更新了 而此时 setState 死循环
- componentWillUnmount 可以  但是在它死之前 没意义啊
- 一般在 componentWillReceiveProps / componentWillMount / 事件监听里


是否决定更新的函数
- shouldComponentUpdate 返回 return false 就不更新
- 极其特殊的情况才用—— 一般用不到
```

### 面试

> 面试：什么情况下用 shouldComponentUpdate 允许我们

手动地判断是否要进行组件更新，根据组件的应用场景设置函数的合理返回值能够帮我们避免不必要的更新

- [知乎专栏](https://zhuanlan.zhihu.com/p/24856035)

- [掘金](https://juejin.im/post/5b2215f76fb9a00e8f795cd1)

> 1.请说出React所有生命周期函数

> 2.请问 shouldComponentUpdate 为什么那么重要

- 可以自定义是否更新组件
- 优化更新的效率

> 3.请问ajax在什么时候用？

- componentDidMount 原因是 constructor 里不能用 setState
- 在 fb里 多次调用会造成不稳定 (其实很多人也不知道为什么fb会造成不稳定)

> 4.用户点击按钮修改state会触发那些周期函数

- shouldComponentUpdate
- componentWillUpdate
- render
- componentDidUpdate