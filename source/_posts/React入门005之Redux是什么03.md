---
title: React入门005之Redux是什么03
date: 2019-01-16 21:21:50
tags:
---

### React-Redux

- [https://react-redux.js.org/](https://react-redux.js.org/)
- 它就是让你不用每曾都传递 store,随时都可以使用store，避免混乱问题

核心就几个东西

- Provider
- connect
- connectAdvanced

我直接拿上一篇的例子复制了一份目录在

- demo-react-redux-2

```
# 安装 react-redux
yarn add react-redux
```

index.js

```
// step1 引入Provider
// step2 用 Provider 包裹App 并传递 store


import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

import { Provider } from 'react-redux'

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

function render(){
  ReactDOM.render(
    <Provider store={store}>
      <App/>
    </Provider>
    , document.getElementById('root'));
  
}

serviceWorker.unregister();
```

> 普及基础知识

```
// 这是啥意思？  一个函数返回值是函数
function xx(a){
    console.log(a)
    return function(b){
        console.log(a + b)
    }
}

xx(1)(2);
// xx()叫做 偏函数 (参数不全)
```


App.js

```
// step1 引入 connect
// step2 connect()(App) 传递App给 connect
import React, { Component } from 'react';

import { connect } from 'react-redux'
class App extends Component {
  render() {
    console.log(this.props);
    return (
        <div className="App">
          <div>
            你点击了<span id="value">{this.props.n}</span>次
            <div>
                <button id="add1" onClick={()=>this.props.add1()}>+1</button>
                <button id="add2">+2</button>
                <button id="add1IfOdd">如果单数就+1</button>
                <button id="addAfter2Ses">2秒后+1</button>
            </div>
          </div>
        </div>
    );
  }
}

/*
 这个 x 就是 把 store里的东西拿出来 放到 this.props上
 所以如果 store = {n:0}传递到这里
 就变成了 this.props.n = 0
 function x(state){
  return{
    n:state.n
  }
}
*/
// 映射 state===>props
/*
// mapStateToProps 必须是一个函数
接受一个参数 state 还有一个不常用的参数
*/
function mapStateToProps(state){
  return{
    n:state.n
  }
}

// 如何 add 呢？ 产生一个action 通过这个connect你就不用 store.dispatch
// 把dispatch映射到 Props
/*
mapDispatchToProps
可以是个函数也可以是个对象
function mapDispatchToProps(dispatch){
  return {
    add1:()=>dispatch({type:'add',payload:1})
  }
}
*/

const mapDispatchToProps = {
  add1:()=>{
    return {type:'add',payload:1}
  }
}

export default connect(mapStateToProps,mapDispatchToProps)(App);
```

index.js

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import * as serviceWorker from './serviceWorker';

import { Provider } from 'react-redux'

import {createStore} from 'redux';
const stateChanger = (state,action)=>{
  if (state === undefined){
    return {n:0}
  }else{
    if(action.type === 'add'){
      var newState ={n:state.n + action.payload}
      return newState;
    }else{
      return state;
    }
  }
}
const store = createStore(stateChanger)

ReactDOM.render(
    <Provider store={store}>
      <App/>
    </Provider>
    , document.getElementById('root'));

serviceWorker.unregister();

```

[代码仓库](https://github.com/slTrust/React-study)

