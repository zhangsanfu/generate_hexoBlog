---
title: React入门010之React的CSS方案
date: 2019-01-24 22:45:01
tags: React入门
---

> ### 传统CSS使用方式

```
<!DOCTYPE html>
<html lang="en">
<head>
    <style>
        #app{
            color:red
        }
    </style>
</head>
<body>
    <div id="app">App</div>
</body>
</html>
```

> ### 在后来 style 标签被提取到一个 css文件里

- 也就是 内容 样式 分离 思想

### React应用的CSS

- 新建一个react应用 [https://codesandbox.io/](https://codesandbox.io/)

index.html

```
<!DOCTYPE html>
<html lang="en">
  <head> </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

styles.css

```
.App {color: red;}
```

index.js

```
import React from "react";
import ReactDOM from "react-dom";

// 引入样式
import "./styles.css";

function App() {
    //  react建议 class属性 使用 className 如果你不加可能依然可以运行。 建议就是按照react的建议做，不要自己作死。
    return <div className="App">Hello World</div>;
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

- [在线代码](https://codesandbox.io/s/mmy3jj903p)

> 这个代码好不好呢？

- 在一开始没什么问题,但是当你的组件逐渐变多，你就觉得不好

```
比如组件嵌套的情况下
都有一个 class="title"
它们之间就会相互影响
```

styles.css

```
.title {
  color: red;
  border: 1px solid red;
  padding: 10px;
}
```

index.js

```
import React from "react";
import ReactDOM from "react-dom";

import "./styles.css";

function Father() {
  return (
    <div className="title">
      父亲组件
      <Sun />
    </div>
  );
}
function Sun() {
  return (
    <div>
      <div className="title">儿子组件</div>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<Father />, rootElement);
```

- [在线代码](https://codesandbox.io/s/x91k4r55lw)

> 两个组件都有title样式 我只想让 <Father /> 字体颜色变红

- 结果 <Sun/> 也变红了

> ### 唯一的办法就是给所有的组件加上一个前缀

如

```
.father-title{ ... }
.sun-title{ ... }
```

> 这个方式叫做—— BEM命名法(思路就是加前缀)

- 为了防止各个组件之间的冲突，我们采用了加前缀的方式解决

styles.css

```
.father-title {
  color: red;
  border: 1px solid red;
  padding: 10px;
}

.sun-title {
  color: green;
  border: 1px solid green;
  padding: 10px;
}

```

index.js 

```
import React from "react";
import ReactDOM from "react-dom";

import "./styles.css";

function Father() {
  return (
    <div className="father-title">
      父亲组件
      <Sun />
    </div>
  );
}
function Sun() {
  return (
    <div>
      <div className="sun-title">儿子组件</div>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<Father />, rootElement);
```

- [在线代码](https://codesandbox.io/s/l40v346mpz)

> 如果你想动态设置主题 theme 

- ES6 字符串的拼接来设置

```
function Sun(props) {
    const theme = props.theme;
    return (
        <div>
            <div className=`sun-title sun-${theme}`>儿子组件</div>
        </div>
    );
}
```

> ### 有没有一种方式让我写多个 class更加的简单一点？

- 于是就有人发明了 [classnames](https://github.com/JedWatson/classnames)
- google 搜 react classnames 得到一个 JedWatson写的库
- 在 https://codesandbox.io 上新建react项目
- 添加依赖 搜索 classnames

styles.css

```
.App {
  font-family: sans-serif;
  text-align: center;
}

.red {
  color: red;
}

.border10 {
  border: 10px solid red;
}
```

index.js

```
import React from "react";
import ReactDOM from "react-dom";
import classNames from "classnames";
import "./styles.css";

function App() {
  return (
    <div className="App">
      <h1 className={classNames("red", "border10")}>Hello CodeSandbox</h1>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

- [在线代码](https://codesandbox.io/s/r0056rmlnq)
- 唯一的感觉就是比之前更麻烦了

> ### 强迫症的心理(处女座)——模块化

每个组件对应一个目录 对应自己的css

- [在线代码](https://codesandbox.io/s/vnj410l9yl)

```
|src
--|topBar
----|topBar.css
----|topBar.js
--|x
----|x.css
----|x.js
--|index.js
```

> 强迫症的执念——我就不想用前缀

1. 两个title 自己分开
2. css:做不到
3. 前端：css go die !

> 既然你css做不到那你就靠边站，我用js

- CSS in JS 方案

### CSS in JS

简单来说这个方案大概有20种

- google 搜索 github css in js
- 你会搜到一个仓库 [css-in-js](https://github.com/MicheleBertoli/css-in-js)
- 里面收录了所有 css in js 的方案 那么有多少呢？
- 大概 60多种，开不开心有60多种

> 此时有一个问题，为什么做前端架构很难呢？

- 没有三年的项目经验
- 没整过这些奇怪的库

> 有没有什么在前端一开始觉得很NB到后面变得很垃圾

- grunt 三年前 三年后
- angular 1.x

> 如何区分这些库呢？  

- 看star数量

> 筛选css in js 的方案

- [styled-components](https://github.com/styled-components/styled-components) 17306 star
- [emotion](https://github.com/emotion-js/emotion)
- [react-css-modules](https://github.com/gajus/react-css-modules)
- [radium](https://github.com/FormidableLabs/radium) 

### styled-components

- https://codesandbox.io 上建立react
- 安装依赖 npm install --save styled-components

index.js

```
import React from "react";
import ReactDOM from "react-dom";
import styled from "styled-components";
import "./styles.css";

const Button = styled.button`
  border-radius: 3px;
  border: 1px solid #666;
  color: #333;
  margin: 0 1em;
  padding: 0.25em 1em;
`;

function App() {
  return (
    <div className="App">
      <Button>按钮</Button>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

- [在线代码](https://codesandbox.io/s/xpwjmyovy4)
- 这样就解决了，你写前缀就浑身难受的心态
- 强迫症是前端的原始动力
- 而且你的世界从此没有了 .css文件

在写一个topbar.js

```
import React from "react";
import styled from "styled-components";

export default function Topbar(props) {
  const theme = props.theme; // 'white | black'

  const onClick = () => {
    console.log(1);
  };
  const Logo = styled.div`
    width: 40px;
    height: 40px;
    background: grey;
  `;
  const Nav = styled.nav`
    height: 30px;
    background: #333;
    width: 200px;
    color: white;
    margin-left: 10px;
  `;
  const Wrapper = styled.div`
    display: flex;
    align-items: center;
    border: 1px solid black;
    padding: 10px;
    background: ${theme};
  `;
  return (
    <Wrapper>
      <Logo>logo</Logo>
      <Nav>nav</Nav>
    </Wrapper>
  );
}
```

> 此时你发现违背了 多年来前端的思想  内容样式分离

- 又回到解放前了
- react 说 我不仅内容混在一起 行为也要混在一起

> styled-components

- 目前react css比较火的方案
- 可以再也看不到css文件
- 不需要定义class
- 而且从工程角度来说——这个方案对新人非常好，不会因为你改一个样式造成整个项目瘫痪了，你只需要关心自己的组件

### emotion方案

- [emotion](https://github.com/emotion-js/emotion)

- 新建 https://codesandbox.io/ 项目
- 安装依赖 @emotion/core"
- x.js

```
/** @jsx jsx */
import React from "react";
import { jsx } from "@emotion/core";

export default function X(props) {
  const theme = props.theme;
  return (
    <div
      css={{
        display: "flex",
        alignItems: "center",
        background: theme,
        padding: 10
      }}
    >
      <div
        css={{
          display: "inline-block",
          color: "red",
          fontSize: 20,
          white: 40,
          height: 40,
          background: "grey"
        }}
      >
        logo
      </div>
      <nav
        css={{
          width: 200,
          height: 30,
          background: "#333",
          marginLeft: 10
        }}
      />
    </div>
  );
}
```

- 有个坑就是第一行必须写那个注释
- 根据与 直接 style="color:red;" 没区别
- 强烈不推荐用

### react-css-modules

- [react-css-modules](https://github.com/gajus/react-css-modules)

- 新建项目 https://codesandbox.io
- 安装依赖 react-css-modules
- 坑来了——需要配置 webpack

我放弃了——为什么webpack还活着

> 如果你成功了，你就可以这样写样式了

```
import React from 'react';
import CSSModules from 'react-css-modules';
import styles from './table.css';

class Table extends React.Component {
    render () {
        return <div styleName='table'>
            <div styleName='row'>
                <div styleName='cell'>A0</div>
                <div styleName='cell'>B0</div>
            </div>
        </div>;
    }
}

export default CSSModules(Table, styles);
```

- [https://github.com/gajus/react-css-modules](https://github.com/gajus/react-css-modules)
- 好处就是 styleName='table' 这个table跟其他的table不会有重叠
- 唯一的改变就是把 className ---> styleName
- webpack就是一步一个坑


### [radium](https://github.com/FormidableLabs/radium) 

- 看文档吧！

```
import Radium from 'radium';
import React from 'react';
import color from 'color';

class Button extends React.Component {
  static propTypes = {
    kind: PropTypes.oneOf(['primary', 'warning']).isRequired
  };

  render() {
    // Radium extends the style attribute to accept an array. It will merge
    // the styles in order. We use this feature here to apply the primary
    // or warning styles depending on the value of the `kind` prop. Since its
    // all just JavaScript, you can use whatever logic you want to decide which
    // styles are applied (props, state, context, etc).
    return (
      <button
        style={[
          styles.base,
          styles[this.props.kind]
        ]}>
        {this.props.children}
      </button>
    );
  }
}

Button = Radium(Button);

// You can create your style objects dynamically or share them for
// every instance of the component.
var styles = {
  base: {
    color: '#fff',

    // Adding interactive state couldn't be easier! Add a special key to your
    // style object (:hover, :focus, :active, or @media) with the additional rules.
    ':hover': {
      background: color('#0074d9').lighten(0.2).hexString()
    }
  },

  primary: {
    background: '#0074D9'
  },

  warning: {
    background: '#FF4136'
  }
};
```

- 单独看例子就——反人类


### 总结四个CSS in JS 方案

```
1. styled-componends      
const Title = styled.div` 样式代码...`

2. emotion
<div css={对象}>

3. css-module   
<div styleName="title">  保证title不对其他组件有影响

4. radium
<div style={[style.title,style.base]}>
```

如果是我我觉得第三个可能是最好的！

> ### 回顾

- 避免 同名 .title 冲突 加前缀
- 不想加前缀，强迫症
- css in js 得到 60多个方案
- 按照star 选了 4个 来实现不写前缀

> ### 建议

```
如果写一个应用 app 
- 用 css-module
- 或者 styled-componends 
因为它们还是在写 css

如果写一个 UI库 lib
- 使用旧的 css 方案 加前缀
- 因为上面的方式会生成随机名字，你的组件样式就没法被覆盖了


.title  --> title_xd0031 这个名字是随机的，没法覆盖

.x-title --> .x-title  不帮你生成名字，可以进行改写和覆盖样式

```