---
title: React入门005之Redux是什么02
date: 2019-01-15 22:16:17
tags: React入门
---

### Redux+React

> 安装 react脚手架工具

```
npm i -g create-react-app@2.1.1

# 新建目录
mkdir demo-react-redux-1
# 切换目录下
cd demo-react-redux-1
# 当前目录新建一个 生成一个react项目
create-react-app .
# 安装成功
yarn start
```

> 编辑 app.js

```
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div className="App">
        <div>
          你点击了<span id="value">0</span>次
          <div>
              <button id="add1">+1</button>
              <button id="add2">+2</button>
              <button id="add1IfOdd">如果单数就+1</button>
              <button id="addAfter2Ses">2秒后+1</button>
          </div>
      </div>
      </div>
    );
  }
}

export default App;
```

实现我们上一篇的雏形

> 引入 redux

```
# 踩坑了
npm i redux
# 因为我们默认使用 yarn install的

# 如果你混着用 就会导致重新 安装所有依赖

# 所以 还是 用yarn
yarn add redux
```


> ### 渲染 store 

index.js

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

import {createStore} from 'redux';
const stateChanger = (state,action)=>{
  if (state === undefined){
    return 0;
  }else{
    if(action.type === 'add'){
      var newState =state + action.payload;
      return newState;
    }else{
      return state;
    }
  }
  return newState;
}
const store = createStore(stateChanger)

// 根节点传递 store
ReactDOM.render(<App value={store.getState()}/>, document.getElementById('root'));

serviceWorker.unregister();
```

App.js

```
// props使用 根结点传递的store
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div className="App">
        <div>
          你点击了<span id="value">{this.props.value}</span>次
          <div>
              <button id="add1">+1</button>
              <button id="add2">+2</button>
              <button id="add1IfOdd">如果单数就+1</button>
              <button id="addAfter2Ses">2秒后+1</button>
          </div>
      </div>
      </div>
    );
  }
}
export default App;
```

> 新问题 点击 add的问题

- store是从props里取的 在App.js里根本拿不到
- 只能继续从 index.js里 传递相应的 事件处理函数

App.js

```
import React, { Component } from 'react';

class App extends Component {
  add1(){
    this.props.onAdd1();
  }
  add2(){
    this.props.onAdd2();
  }
  add3(){
    this.props.onAdd3();
  }
  add4(){
    this.props.onAdd4();
  }
  render() {
    return (
      <div className="App">
        <div>
          你点击了<span id="value">{this.props.value}</span>次
          <div>
              <button id="add1" onClick={()=>this.add1()}>+1</button>
              <button id="add2" onClick={()=>this.add2()}>+2</button>
              <button id="add1IfOdd" onClick={()=>this.add3()}>如果单数就+1</button>
              <button id="addAfter2Ses" onClick={()=>this.add4()}>2秒后+1</button>
          </div>
      </div>
      </div>
    );
  }
}

export default App;
```

index.js

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

import {createStore} from 'redux';
const stateChanger = (state,action)=>{
  if (state === undefined){
    return 0;
  }else{
    if(action.type === 'add'){
      var newState =state + action.payload;
      return newState;
    }else{
      return state;
    }
  }
  return newState;
}
const store = createStore(stateChanger)

render();

//render不传递参数
store.subscribe(render)

/*
//render传递参数
store.subscribe(()=>{
  render(参数)
})
*/

function add3(){
  if(store.getState()%2 === 1){
    store.dispatch({type:'add',payload: 1})
  }
}

function add4(){
  setTimeout(() => {
    store.dispatch({type:'add',payload: 1})
  }, 2000);
}

// 传递每个修改store的处理函数,因为App.js里 没法获取操作 store
function render(){
  ReactDOM.render(
    <App value={store.getState()}
      onAdd1={()=>{store.dispatch({type:'add',payload: 1})}}
      onAdd2={()=>{store.dispatch({type:'add',payload: 2})}}
      onAdd3={add3}
      onAdd4={add4}
    />, document.getElementById('root'));
  
}

serviceWorker.unregister();
```

> #### 对比 Redux+VanillaJS 和 Redux+React

```
Redux + VanillaJS 每次点击按钮 整体 render

Redux + React 每次点击按钮 只更新包含数字的span

```

- 这就是React的好处 让你每次不需要整体更新div,它通过dom diff 找该更新的地方


#### 尽管如此 Redux+React的写法依然很恶心

- 需要根节点把action的函数传递下去

```
// index.js里
<App value={store.getState()}
      onAdd1={()=>{store.dispatch({type:'add',payload: 1})}}


// App.js里
class App extends Component {
  add1(){
    this.props.onAdd1();
    //为什么不能直接在这里  派发action呢？
    // store.dispatch()
  }
}
```

> 有一个办法就是在根节点传递 store

```
<App value={store.getState()}
     store={store}
/>

//这样 App.js里就可以
store.dispatch()
```

> #### 但是
> #### 但是
> #### 但是

虽然不用一层一层的往上调用，但是需要一层一层的往下传递store

假设 App内还有组件  就要每次都往子元素里传递

> 解决方案 React-Redux

注意是 React-Redux 跟 Redux是不同的

详情见下一篇

[代码仓库](https://github.com/slTrust/React-study)

