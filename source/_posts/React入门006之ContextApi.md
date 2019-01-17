---
title: React入门006之ContextApi
date: 2019-01-16 22:43:56
tags: React入门
---

### 预习内容

- [Context](https://reactjs.org/docs/context.html)
- [Context中文版](https://react.docschina.org/docs/context.html#when-to-use-context)

> 在线IDE

- [CodeSandBox](https://codesandbox.io/)

> #### 先看代码

```
function f1() {
  console.log(1);
  f2();
}
function f2() {
  console.log(2);
  f3();
}
function f3() {
  console.log(3);
  f4();
}
function f4() {
  console.log(4);
}
f1();
console.log("done");

/*
1
2
3
4
done
*/
```

再看

> f1多了一个 参数 n1
 我想要后面的每一个函数都能获取这个 n1
 咋办？

```
function f1(n1) {
  console.log(1,n1);
  f2(n1);
}
function f2(n2) {
  //这里的 n2就是 f1里的 n1
  console.log(2,n2);
  f3(n2);
}
function f3(n3) {
  console.log(3,n3);
  f4(n3);
}
function f4(n4) {
  console.log(4,n4);
}

{
    let n = 100;
    f1( n );
    console.log("done");
}
/*
函数里的函数 得到n
现在则需要 层层传递
*/
```

- [VanillaJS版本](https://codesandbox.io/s/l9rrqj8zyz)


> react 4层组件里实现上面的逻辑

- [React版本](https://codesandbox.io/s/jp647nl355)

index.js

```
import React from "react";
import ReactDOM from "react-dom";

import "./styles.css";

function F1(props) {
  return (
    <div>1111,{props.n1}
      <F2 n2={props.n1}></F2>
    </div>
  );
}
function F2(props) {
  return (
    <div>2222,{props.n2}
      <F3 n3={props.n2}></F3>
    </div>
  );
}
function F3(props) {
  return (
    <div>3333,{props.n3}
      <F4 n4={props.n3}></F4>
    </div>
  );
}
function F4(props) {
  return (
    <div>4444,{props.n4}
    </div>
  );
}    
class App extends React.Component {
  constructor() {
    super();
    this.state = {
      n: 99
    };
  }
  render() {
    return (
      <div>
        <F1 n1={this.state.n}/>
      </div>
    );
  }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);

```

这样很麻烦需要层层传递

> 如果 F4想单独拿到 n呢？

```
// 唯一的办法将 n提取到外部
function f1() {
  console.log(1);
  f2();
}
function f2() {
  //这里的 n2就是 f1里的 n1
  console.log(2);
  f3();
}
function f3() {
  console.log(3);
  f4();
}
function f4() {
  console.log(4,n);
}

let n = 100;
{
    f1( n );
    console.log("done");
}
/*
f4单独 得到n 不想层层传递
现在则需要 将 n放在全局
*/
```

如果将 n 放在全局就回导致容易被篡改

- 全局变量慎用

> #### 改良： 局部的全局变量

```
{
    let x = {};
    window.setX = function(key,value){
        x[key] = value
    }

    window.f1 = function f1() {
        console.log(1);
        f2();
    }

    window.f2 = function f2() {
    //这里的 n2就是 f1里的 n1
        console.log(2);
        f3();
    }

    window.f3 = function f3() {
        console.log(3);
        f4();
    }

    window.f4 = function f4() {
        console.log(4,x['n']);
    }
}

{   
    window.setX('n',100)
    setTimeout(() => {
        window.f1();        
    }, 1000);
    console.log("done");
}
```

- 这个 x 就叫 context
- setX 就是 setContext
- 他就是对于组件来使用的——局部的全局变量
- 避免了 层层传递props

> #### 用Context 改写 React例子

style.css

```
.box {
  border: 1px solid red;
  padding: 10px;
}

```

index.js

```
import React from "react";
import ReactDOM from "react-dom";

import "./styles.css";

function F1() {
  return (
    <div className="box">
      1111
      <F2 />
    </div>
  );
}
function F2() {
  return (
    <div className="box">
      2222
      <F3 />
    </div>
  );
}
function F3() {
  return (
    <div className="box">
      3333
      <nContext.Consumer>{xxx => <F4 n4={xxx} />}</nContext.Consumer>
    </div>
  );
}
function F4(props) {
  return <div className="box">4444,{props.n4}</div>;
}

const nContext = React.createContext();

class App extends React.Component {
  render() {
    return (
      <div>
        <nContext.Provider value="999">
          <F1 />
        </nContext.Provider>
      </div>
    );
  }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

- [React版本](https://codesandbox.io/s/jny7mn8415)

> 理解代码

JSX

```
<div></div> 
//翻译成
React.createElement('div',null)


// ------------------------------------
<div className="x" title="hi"></div> 
//翻译成
React.createElement('div',{className:'x',title:'hi'})


// ------------------------------------
<div className="x" title="hi">aaaa</div> 
//翻译成
React.createElement('div',{className:'x',title:'hi'},'aaaa')


// ------------------------------------
function F1(){
    return '1111'
}
<div className="x" title="hi">
    <F1></F1>
</div> 

//翻译成 当你用标签写的时候 永远会在 前面加上一个React.createElement
React.createElement(
    'div',{className:'x',title:'hi'},
    React.createElement(F1,null)
)

// ------------------------------------
function F1(){
    return '1111'
}
<div className="x" title="hi">
    F1
    {F1}
    <F1/>
</div> 
//翻译成 
//当你写F1就是 字符串 "F1" 
//当你用标签写的时候 永远会在 前面加上一个React.createElement
React.createElement(
    'div',{className:'x',title:'hi'},
    'F1',
    F1, //这是个函数 但是React并不知道怎么用
    React.createElement(F1,null)
)
```

> #### 实现一个接受函数的组件——(Consumer原理)

index.js

```
import React from "react";
import ReactDOM from "react-dom";

// Ccc就是 Consumer
function Ccc(props) {

  /* 
  props.children 可以获取 Ccc里得内容 <Ccc>{fn}<Ccc/>
  如果 传递一个函数
  props.children 就是那个函数 fn
  传递 <Ccc>{fn}{fn2}<Ccc/>
  那么 props.children 就是 [fn,fn2]
  */
  
  let info = 100;
  let result = props.children(info);
  return <div>{result}</div>;
}

function App() {
  return (
    <div>
      <Ccc c1="c1">
        {msg => {
          console.log(`我得到的信息是${msg}`);
          return <div>我得到的数据是：{msg}</div>;
        }}
      </Ccc>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);

```

- [代码地址](https://codesandbox.io/s/wwp6kkxno5)

