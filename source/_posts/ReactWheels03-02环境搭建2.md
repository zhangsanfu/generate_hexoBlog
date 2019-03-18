---
title: ReactWheels03-02环境搭建2
date: 2019-03-18 21:24:48
tags: ReactWheels
---

### 环境搭建2

> #### git小技巧 如何将代码 分别push 到不同仓库

- 打开你的 [github](https://github.com) 新建一个空仓库 hjx-test-2
- 我上次的代码仓库是 [hjx-test-1](https://github.com/slTrust/hjx-test-2)

    ```
    跑到你上次的代码仓库目录 打开命令行
    # git remote add [随便一个名字] git@github.com:slTrust/hjx-test-2.git
    git remote add xxx git@github.com:slTrust/hjx-test-2.git
    
    git push -u xxx master
    ```
- 此时 [https://github.com/slTrust/hjx-test-2](https://github.com/slTrust/hjx-test-2) 就有你另一个仓库的所有代码了


> 修改 package.json 文件

```
"scripts": {
    "start":"webpack-dev-server",
    "build":"webpack"
},

这样我们以后就可以

yarn start
yarn build
```

### 添加 button.tsx

lib/button.tsx

```
function Button(){
    return (
        <div>
            按钮
        </div>
    )
}

export default Button
```

index.tsx

```
import React from 'react';
import ReactDom from 'react-dom';

console.log(React)
console.log(ReactDom)
```

安装依赖

```
yarn add react react-dom 
# 为保持一直最好跟我一个版本
yarn add react@16.8.4 react-dom@16.8.4


# 运行我们的项目
yarn start

报错
ERROR in [at-loader] ./lib/index.tsx:1:19 
    TS7016: Could not find a declaration file for module 'react'. '/Users/huangjiaxi/Desktop/hjx-test-1/node_modules/react/index.js' implicitly has an 'any' type.
  Try `npm install @types/react` if it exists or add a new declaration (.d.ts) file containing `declare module 'react';`

ERROR in [at-loader] ./lib/index.tsx:2:22 
    TS7016: Could not find a declaration file for module 'react-dom'. '/Users/huangjiaxi/Desktop/hjx-test-1/node_modules/react-dom/index.js' implicitly has an 'any' type.
  Try `npm install @types/react-dom` if it exists or add a new declaration (.d.ts) file containing `declare module 'react-dom';`

google 搜索 Could not find a declaration file for module 'react'

点进入第一个url https://stackoverflow.com/questions/41292559/could-not-find-a-declaration-file-for-module-module-name-path-to-module-nam

里面有个赞多的 说这个
npm install -D @types/module-name

由于我们用的 yarn 所以
yarn add @types/react --dev

继续运行 
yarn start 
还是报错
Could not find a declaration file for module 'react-dom'

根据上次的问题
yarn add @types/react-dom --dev

再次运行 yarn start 成功
```

> #### 在TS项目里 除了安装源代码之外还要安装它的类型声明文件

- 一般默认帮你安装
- 如果没安装，你需要手动安装
    ```
    # 如果你安装它们
    yarn add react react-dom --dev
    # 就要安装 类型声明文件
    yarn add @types/react @types/react-dom --dev

    ```

> #### 修改 index.tsx

```
import React from 'react';
import ReactDom from 'react-dom';
import Button from './button'

ReactDom.render(<Button></Button>,document.body);
```

运行 yarn start 结果报错
Can't resolve './button' in '/Users/huangjiaxi/Desktop/hjx-test-1/lib'

- 因为tsx的后缀不支持
- 修改webpack.config.js
- 添加 resolve

```
entry: {
    ...
},
resolve:{
    extensions:['.ts','.tsx','.js','.jsx']
},
output:{
    ...
}
```

```
# 继续运行
yarn start

# 又报错了 说 React 是一个 全局变量
ERROR in [at-loader] ./lib/button.tsx:3:10 
    TS2686: 'React' refers to a UMD global, but the current file is a module. Consider adding an import instead.


# 原因是 你写的 button.tsx
function Button(){
    return (
        <div>
            按钮
        </div>
    )
}

return 的内容  里的 div 会被翻译成 
React.createElement('div',null,'按钮')
而 button.tsx 里 没有 React
```

再次运行 yarn start 成功

> #### 但是给了一个警告(意思是推荐一个文件244K但是你的文件 259K)

```
WARNING in asset size limit: The following asset(s) exceed the recommended size limit (244 KiB).
This can impact web performance.
Assets: 
  index.js (259 KiB)

WARNING in entrypoint size limit: The following entrypoint(s) combined asset size exceeds the recommended limit (244 KiB). This can impact web performance.
Entrypoints:
  index (259 KiB)
      index.js

WARNING in webpack performance recommendations: 
You can limit the size of your bundles by using import() or require.ensure to lazy load some parts of your application.
For more info visit https://webpack.js.org/guides/code-splitting/
```

- 但是我们的index.tsx 里就写了那么几行怎么会那么多呢?
- 还记得我们 webpack.config.js 里的 mode 吗
    ```
    # 修改 webpack.config.js 里的 mode
    mode:'development'

    # 再次运行
    yarn start 
    警告消失了

    # 为什么？ 因为在 development 时 认为你是在开发模式，所以不会警告文件大的问题
    ```

> #### 理解 mode的区别

- 影响 webpack 的提示，如果是 production 就提示文件过大，如果是 development 就不提示
- development  不压缩代码
- production 压缩所有代码变成一行


> #### 如何解决打包文件过大呢？

- 不打包React 
    ```
    # 修改 mode
    mode:'production',
    # 添加 externals（不打包的内容）
            {
            commonjs:'react',
            commonjs2:'react',
            amd:'react',
            root:'React'
            }
        代表针对不同的打包模式
    ```

```
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    mode:'production',
    entry: {
        index: './lib/index.tsx'
    },
    resolve:{
        extensions:['.ts','.tsx','.js','.jsx']
    },
    output:{
        path:path.resolve(__dirname,'dist/lib'),
        library:'FUI',
        libraryTarget:'umd'
    },
    module:{
        rules:[
            {
                test:/\.tsx?$/,
                loader: 'awesome-typescript-loader'
            }
        ]
    },
    plugins:[
        new HtmlWebpackPlugin({
            template:'index.html'
        })
    ],
    externals:{
        react:{
            commonjs:'react',
            commonjs2:'react',
            amd:'react',
            root:'React'
        },
        'react-dom':{
            commonjs:'react-dom',
            commonjs2:'react-dom',
            amd:'react-dom',
            root:'ReactDom'
        }
    }
}
```

### 如何来动态切换 各种模式呢?

- webpack.config.js
    ```
    const path = require('path')
    const HtmlWebpackPlugin = require('html-webpack-plugin')
    module.exports = {
        entry: {
            index: './lib/index.tsx'
        },
        resolve:{
            extensions:['.ts','.tsx','.js','.jsx']
        },
        output:{
            path:path.resolve(__dirname,'dist/lib'),
            library:'FUI',
            libraryTarget:'umd'
        },
        module:{
            rules:[
                {
                    test:/\.tsx?$/,
                    loader: 'awesome-typescript-loader'
                }
            ]
        }
    }
    ```
- 新建 webpack.config.dev.js 
    ```
    const base = require('./webpack.config')
    const path = require('path')
    const HtmlWebpackPlugin = require('html-webpack-plugin')
    module.exports = Object.assign({},base,{
        mode:'development',
        plugins:[
            new HtmlWebpackPlugin({
                template:'index.html'
            })
        ]
    })
    ```
- 新建 webpack.config.prod.js
    ```
    const base = require('./webpack.config')
    const path = require('path')
    const HtmlWebpackPlugin = require('html-webpack-plugin')
    module.exports = Object.assign({},base,{
        mode:'production',
        externals:{
            react:{
                commonjs:'react',
                commonjs2:'react',
                amd:'react',
                root:'React'
            },
            'react-dom':{
                commonjs:'react-dom',
                commonjs2:'react-dom',
                amd:'react-dom',
                root:'ReactDom'
            }
        }
    })
    ```
- package.js
    ```
    "scripts": {
        "start": "webpack-dev-server --config webpack.config.dev.js",
        "build": "webpack --config webpack.config.prod.js"
    },
    ```

- 高级处理
```
你可能看到别人的package.json里是这样的
cross-env NODE_ENV=development webpack-dev-server --config webpack.config.dev.js
cross-env NODE_ENV=production webpack --config webpack.config.prod.js

# 它是跨平台用的(兼容windows)，能保证你加的环境变量在各个平台都可以被理解

# 需要安装依赖
yarn add cross-env --dev

```


