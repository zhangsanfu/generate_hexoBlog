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

> #### lib/index.d.ts 是干嘛的

给使用我们库的人，我们生成了那些类型，但是它应该在 dist目录里

```
# 添加 
outDir:"dist"

修改 tsconfig.json
{
    "compilerOptions": {
      "outDir":"dist",
      "declaration": true,
      "baseUrl": ".",
      "module": "esnext",
      "target": "es5",
      "lib": ["es6", "dom"],
      "sourceMap": true,
      "jsx": "react",
      "moduleResolution": "node",
      "rootDir": ".",
      "forceConsistentCasingInFileNames": true,
      "noImplicitReturns": true,
      "noImplicitThis": true,
      "noImplicitAny": true,
      "importHelpers": true,
      "strictNullChecks": true,
      // "suppressImplicitAnyIndexErrors": true,
      // https://github.com/Microsoft/TypeScript/issues/28762#issuecomment-443406607
      "allowSyntheticDefaultImports": true,
      "noUnusedLocals": true
    },
    "include": [
    ],
    "exclude": [
      "node_modules",
      "build",
      "dist",
      "scripts",
      "acceptance-tests",
      "webpack",
      "jest",
      "src/setupTests.ts",
      "*.js"
    ]
  }

# 运行
yarn build 
发现 之前的文件都生成在 dist里了
index.d.ts
button.d.ts

button.d.ts是多余的如何去掉呢？

修改package.json

"main": "dist/lib/index.js",
"types":"dist/lib/index",
```

### 配置react 测试框架 jest

google 搜索 [react jest](https://jestjs.io/docs/en/tutorial-react)

```
yarn add --dev jest babel-jest @babel/preset-env @babel/preset-react react-test-renderer

# 新建 .babelrc

{
    "presets":["react-app"]
}

# 在 package.json scripts里新增测试命令
"test":"cross-env NODE_ENV=test jest --config=jest.config.js --runInBand"

# 新建 jest.config.js 文件

// https://jestjs.io/docs/en/configuration.html

module.exports = {
    verbose: true,
    clearMocks: false,
    collectCoverage: false,
    reporters: ["default"],
    moduleFileExtensions: ['js', 'jsx', 'ts', 'tsx'],
    moduleDirectories: ['node_modules'],
    moduleNameMapper: {
      "\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$": "<rootDir>/test/__mocks__/file-mock.js",
    },
    testMatch: ['<rootDir>/**/__tests__/**/*.unit.(js|jsx|ts|tsx)'],
    transform: {
      "^.+unit\\.(js|jsx)$": "babel-jest",
      '^.+\\.(ts|tsx)$': 'ts-jest',
    },
    setupFilesAfterEnv: ["<rootDir>test/setupTests.js"]
  }

# 运行 yarn test

报错ts-jest in the transform option was not found.

# 安装 ts-jest
yarn add --dev ts-jest

# 继续运行 yarn test
 
 又报错了
 Module <rootDir>test/setupTests.js in the setupFilesAfterEnv option was not found.
         <rootDir> is: /Users/huangjiaxi/Desktop/hjx-test-1

# 新建 test/setupTests.js

# 再次运行 yarn test
不报错了 提示没找到任何 test文件 走了
No tests found, exiting with code 1

# 新建文件 ./lib/__tests__/hello.unit.tsx

test('hello',()=>{ })

# 此时 test 标红 意思是不知道是啥 你要安装依赖
yarn add @types/jest --dev

# test()不标红了


# 修改 hello.unit.tsx

test('hello',()=>{
    expect(1).toEqual(2)
})

# 运行 yarn test 还是爆错

# 用 describe

describe('我的第一个测试用例',()=>{
    it('1 等于 1',()=>{
        expect(1).toEqual(2)
    })
})

# 运行 yarn test 测试生效了
 FAIL  lib/__tests__/hello.unit.tsx
  我的第一个测试用例
    ✕ 1 等于 1 (6ms)

  ● 我的第一个测试用例 › 1 等于 1

    expect(received).toEqual(expected)

    Expected: 2
    Received: 1

      1 | describe('我的第一个测试用例',()=>{
      2 |     it('1 等于 1',()=>{
    > 3 |         expect(1).toEqual(2)
        |                   ^
      4 |     })
      5 | })

      at Object.<anonymous> (lib/__tests__/hello.unit.tsx:3:19)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        2.444s


# 修改 测试内容 并运行 yarn test

describe('我的第一个测试用例',()=>{
    it('1 等于 1',()=>{
        expect(1).toEqual(1)
    })
})

// 测试通过
 PASS  lib/__tests__/hello.unit.tsx
  我的第一个测试用例
    ✓ 1 等于 1 (3ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        2.552s
Ran all test suites.
✨  Done in 3.32s.

```

> #### 测试 我们的button 能渲染

button.unit.tsx

```
import renderer from 'react-test-renderer'
import React from 'react'
import Button from '../button'

describe('button',()=>{
    it('是个 div',()=>{
        const json = renderer.create(<Button/>)
            .toJSON()

        expect(json).toMatchSnapshot()
    })
})

// 运行 yarn test
报错 提示
Try `npm install @types/react-test-renderer` if it exists or add a new declaration (.d.ts) file containing `declare module 'react-test-renderer';`

// 安装 类型依赖
yarn add --dev @types/react-test-renderer

# 继续运行 yarn test
又报错
Cannot read property 'create' of undefined
```


> #### 神奇的 import

上面之所以失败是因为 jest 写 import必须这样

button.unit.tsx

```
import * as renderer from 'react-test-renderer'
import * as React from 'react'
import * as Button from '../button'

describe('button',()=>{
    it('是个 div',()=>{
        const json = renderer.create(<Button/>)
            .toJSON()

        expect(json).toMatchSnapshot()
    })
})
```

button.tsx

```
import * as React from 'react';
function Button(){
    return (
        <div>
            按钮
        </div>
    )
}
export default Button
```

> #### 不想要这个 import * as 怎么办

tsconfig.test.js

```
{
    "extends":"./tsconfig.json",
    // "compilerOptions":{
        // "module":"commonjs"
    // }
}
```

jest.config.js

```
// https://jestjs.io/docs/en/configuration.html

module.exports = {
    verbose: true,
    clearMocks: false,
    collectCoverage: false,
    reporters: ["default"],
    moduleFileExtensions: ['js', 'jsx', 'ts', 'tsx'],
    moduleDirectories: ['node_modules'],
    globals: {
        'ts-jest': {
          tsConfig: 'tsconfig.test.json',
        },
    },
    moduleNameMapper: {
      "\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$": "<rootDir>/test/__mocks__/file-mock.js",
    },
    testMatch: ['<rootDir>/**/__tests__/**/*.unit.(js|jsx|ts|tsx)'],
    transform: {
      "^.+unit\\.(js|jsx)$": "babel-jest",
      '^.+\\.(ts|tsx)$': 'ts-jest',
    },
    setupFilesAfterEnv: ["<rootDir>test/setupTests.js"]
  }
```

jsconfig.json

```
{
    "compilerOptions": {
      "outDir":"dist",
      "declaration": true,
      "baseUrl": ".",
      "module": "esnext",
      "target": "es5",
      "lib": ["es6", "dom"],
      "sourceMap": true,
      "jsx": "react",
      "moduleResolution": "node",
      "rootDir": ".",
      "forceConsistentCasingInFileNames": true,
      "noImplicitReturns": true,
      "noImplicitThis": true,
      "noImplicitAny": true,
      "importHelpers": true,
      "strictNullChecks": true,
      // "suppressImplicitAnyIndexErrors": true,
      // https://github.com/Microsoft/TypeScript/issues/28762#issuecomment-443406607
      // "allowSyntheticDefaultImports": true,
      "esModuleInterop": true,
      "noUnusedLocals": true
    },
    "include": [
    ],
    "exclude": [
      "node_modules",
      "build",
      "dist",
      "scripts",
      "acceptance-tests",
      "webpack",
      "jest",
      "src/setupTests.ts",
      "*.js"
    ]
  }
```

tslint.json

```
{
    "extends": ["tslint:recommended", "tslint-react"],
    "rules": {
      "no-console": [false, "log", "error"],
      "jsx-no-multiline-js": false,
      "whitespace": false,
      "no-empty-interface": false,
      "space-before-function-paren": false,
      "no-namespace": false,
      "label-position": false,
      "quotemark": [true, "single", "jsx-double"],
      "member-access": false,
      "semicolon": [true, "always", "ignore-bound-class-methods"],
      "no-unused-expression": [true, "allow-fast-null-checks"],
      "member-ordering": false,
      "trailing-comma": false,
      "arrow-parens": false,
      "jsx-self-close": false,
      "max-line-length": false,
      "interface-name": false,
      "no-empty": false,
      "comment-format": false,
      "ordered-imports": false,
      "object-literal-sort-keys": false,
      "eofline": false,
      "jsx-no-lambda": false,
      "no-trailing-whitespace": false,
      "jsx-alignment": false,
      "jsx-wrap-multilines": false,
      "no-shadowed-variable": [
        false,
        {
          "class": true,
          "enum": true,
          "function": false,
          "interface": false,
          "namespace": true,
          "typeAlias": false,
          "typeParameter": false
        }
      ]
    },
    "linterOptions": {
      "exclude": [
        "config/**/*.js",
        "node_modules/**/*.ts",
        "coverage/lcov-report/*.js"
      ]
    }
  }
```

