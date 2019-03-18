---
title: ReachWheels03-01环境搭建
date: 2019-03-16 22:10:59
tags: ReactWheels
---

### 如何快速学会项目搭建

- 不可能

 只能随机应变，遇到问题就去搜相关 issues 文档和讨论(英文)

> 步骤

1. 创建目录和远程仓库
2. npm初始化
3. 新建 lib/index.tsx
4. 新建 webpack.config.js
    1. 配置 entry
    2. 配置 output
    3. 配置 module.rules
        - jsx
        - tsx
        - scss
    4. 配置 plugins
5. 配置 webpack-dev-server 和 webpack.config.dev.js
6. 创建 index.html
7. 配置 webpack.config.prod.js
8. 创建 examples 预览 与 webpack.config.docs.js
9. 引入测试
10. 引入 CI
11. 配置 tsconfig.json 和 tslint.json
12. 配置 scripts
    1. yarn start
    2. yarn build
13. 自定义任务
    1. yarn task create component xxx 快捷入口


#### 实现步骤

1. 创建远程仓库 如 hjx-test-1 之后 复制 仓库地址
    ```
    把仓库拉到你本地 
    cd ~/Desktop  
    git clone git@github.com:slTrust/hjx-test-1.git
    进入你的仓库目录
    cd hjx-test-1
    ```
2. 初始化 package.json
    ```
    npm init -y 回车

    安装 webpack   https://webpack.js.org/  找到 get started 点进去
        npm install webpack webpack-cli --save-dev
        但是最好不要用 npm 用 yarn   
        // --dev 就是开发者依赖
        // yarn add webpack webpack-cli --dev
        // 为保持一直 跟我一个版本
        yarn add webpack@4.29.6  webpack-cli@3.2.3 --dev
    ```
3. 新建 lib/index.tsx
    ```
    csonole.log('hi')
    ```
4. 新建 webpack.config.js
    ```
    module.exports = {
        entry: {
            index: './lib/index.tsx'
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

    // 正则  /\.tsx?$/  代表匹配 .tsx 结尾或者 .ts 结尾的

    // 配置输出路径  
    本来想这样  ./dist 
    但是不知道为什么 都会引入一个path
        const path = require('path')
        ...
        output:{
            path:__dirname + '/dist'
        }
        ...
    // 此时有一个问题  不能这样 因为不同操作系统是目录的分隔符不同的   
        - mac /  
        - windows \
    // 所以应该这样
    path:path.resolve(__dirname,'dist/lib')

    // 我们输出的 文件应该有个文件名 实际就是 entry 里的 key

    output:{
        path:path.resolve(__dirname,'dist/lib'),
        library:'FUI',
        libraryTarget:'umd'
    },

    // umd 是什么
    以前 前端 很分裂的
    出现过 require.js  来加载模块 它是 amd 的
    后来 nodejs 出了自己的模块化  它是 commonjs 规范

    require.js 只在浏览器使用
    nodejs 只在 node 里用

    于是你用的时候 就很烦 如何区分你是浏览器还是 node呢？ 
    - umd 出现了 统一模块定义
    - 所谓 umd 就是根据一些特征选择 不同的定义方式
        if(define)
            // require.js
        else if (module)
            // commonJS
    ```
    
    webpack.config.js

    ```
    const path = require('path')
    module.exports = {
        entry: {
            index: './lib/index.tsx'
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

    运行 npx webpack 
    ```
    报错 awesome-typescript-loader 不存在
    yarn awesome-typescript-loader@5.2.1
    报错，说你没安装 ts
    yarn add typescript@3.3
    ```

    新建 tslint.json

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

    新建 tsconfig.json

    ```
    {
        "compilerOptions": {
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
    ```

    npx webpack 

    ```
    报错 mode 模式没定义
    // 先定义为 生产环境
    mode:'production',

    继续 运行
    npx webpack 成功
    ```

#### npm install 参数的区别

```
npm install --save  给用户用

npm install --save-dev  只给程序员用


React  --save
Jquery  --save

Webpack  -dev
ts -dev

如果你不加 默认 是 --save 的
```

> 缩写

```
--save  简写 -S
--dev 简写 -D
```

> #### 如果你用yarn

```
yarn add  <===> npm install --save
yarn add --dev  <===> npm install --save-dev
```

#### umd是干啥的？

告诉代码封装成什么样的代码定义

- commonjs
- amd
- umd 兼容前两种


### 如何做到自动打包呢？

webpack-dev-server

```
# 安装  webpack-dev-server
yarn add webpack-dev-server --dev

# 为保持一直我的版本是这个
yarn add webpack-dev-server@3.2.1 --dev


安装完成后运行
npx webpack-dev-server

默认给你一个网址 http://localhost:8080/

http://localhost:8080/index.js 
改动一下index.js发现代码就改变了(注意要自己刷新浏览器)
```

> #### webpack-dev-server 是干嘛的？

- webpack 是 把一个1.tsx 打包为 1.js
- webpack-dev-server
    ```
    会开一个server 默认是 http://localhost:8080/
    然后
        你访问 http://localhost:8080/index.js  时
        去找 index.js发现没有 但是发现有个 index.tsx
        于是把 index.tsx 翻译成 index.js的字符串 放在内存里
        注意此时的 index.js 并不是 dist里的文件 

    ```

> 此时我们还是无法继续写代码，因为只有js 没有 html

### 如何引入html文件呢

- 如果我们自己创建一个 index.html 里面引入 ./dist/lib/index.js 
- index.js 的文件名你想改咋办，它是跟 webpack.config.js 里的 entry里 写的 
- 改要改两个地方

```
<!DOCTYPE html>
<html lang="en">
<head></head>
<body>
    <script src="./dist/lib/index.js"></script>
</body>
</html>
```

> #### 自动写入index.js

- #### html-webpack-plugin 改写 html的插件

```
<!DOCTYPE html>
<html lang="en">
<head></head>
<body>
    <!-- 由插件写入 -->
</body>
</html>
```

> 过程

```
yarn add html-webpack-plugin@3.2.0 --dev

改写 webpack.config.js

const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    mode:'production',
    entry: {
        index: './lib/index.tsx'
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
    ]
}

修改 index.tsx

console.log('hi4');
const div = document.createElement('div')
div.innerText = 'div'
document.body.appendChild(div);

重新运行 
npx webpack-dev-server 刷新浏览器 里面就有了 div

```

