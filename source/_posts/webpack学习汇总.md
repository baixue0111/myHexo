---
title: webpack 学习汇总
date: 2018-03-05
tags: [webpack]
categories: 
        - webpack
---
## 什么是webpack?
webpack是一个模块打包器。它将根据模块的依赖关系进行静态分析，然后将这些模块按照指定的规则生成对应的静态资源。其主要完成三件功能：
* 打包： 可以把多个javaScript文件打包成一个文件，减少服务器压力和下载带宽。
* 转换：把拓展语言转换为普通的javaScript，浏览器可以识别的语言。（如：Less、Sass、TyptScript等）。
* 优化： 随着前端越来越复杂，webpack也增加了优化功能，提高性能。
![](/images/what-is-webpack.png)
## 安装webpack
``` npm
cd webpackDemo                          // 进入到webpack项目目录
npm install webpack --save-dev
```
<font color=#CB1623>注意： 官方不推荐全局安装webpack，会将您项目中的webpack锁定到指定版本，并且在使用不同的webpack版本的项目中，会导致构建失败。</font>
<!-- more -->
参数解释： 
* --save: 保存到package.json文件中
* -dev：表示开发时使用这个包，而生产环境不用。（也就是保存到package.json文件中的"devDependencies"字段下）
### 开发环境和生产环境：
开发环境：程序员在创建到完成项目中使用的依赖包
生产环境：程序完成后要上线运行的环境称为生产环境，此环境下使用的依赖包。
**此时可能会出现报错的原因：**
1、 node版本问题：更新node版本。
2、 网络问题：使用npm安装网速过慢会导致安装错误，这个情况下可以使用淘宝镜像cnpm（https://npm.taobao.org/mirrors/npm/）安装，使用之前先进行安装cnpm。
3、 权限问题：在Linux、Ma环境下安装才需要权限，如果是windows只需要管理员权限安装即可。
### 检查webpack是否安装成功
``` npm
webpack -v
```
如果执行此命令出现的版本号，表示安装成功。
## 配置文件
配置文件webpack.config.js是需要手动在项目的根目录下创建的，创建好之后对其进行配置，基本结构如下：
``` JavaScript
// webpack.config.js 文件
module.exports = {
    entry: {},
    output: {},
    module: {},
    plugins: [],
    devServer: {}
}
```
* entry: 入口文件配置（可以是单入口，也可以是hi多入口）
* output: 出口文件配置，webpack2.X版本后支持支持多出口配置
* module: 模块，处理各种Loader（转换css、js、图片等）
* plugins: 插件配置
* devServer: 配置webpack开发服务功能(热打包)

## 多入口、多出口的相关配置
``` JavaScript
const path = require("path");
module.exports = {
    entry: {  // 入口文件配置(入口文件是自己的本地文件)
        entry: "./src/entry.js",
        entry2: "./src/entry2.js"
    },
    output: {
        path: path.resolve(__dirname, "dist"),  // 出口文件路劲，使用了node语法，所以头部需要引入path
        filname: '[name].js'   // 输出文件的名称 [name]的意思是根据入口文件的名称打包成相同的名称，有几个入口文件就可以打包出几个文件
    },
    module: {},
    plugins: [],
    devServer: {}
}

```
## 配置服务与热打包
### 首先安装webpack-dev-server服务：
``` JavaScript
npm install webpack-dev-server --save-dev
```
### 配置webpack服务
``` JavaScript
module.exports = {
    // 入口配置
    entry: {},
    // 出口配置
    output: {},
    // 模块 loader
    module: {},
    // 插件
    plugins: [],
    // 服务
    devServer: {
        contentBase: path.resolve(__dirname, "dist"),   // 配置服务器基本运行路径。用于找到程序打包地址。
        host: '172.16.0.223',   // 服务器运行地址，可以是IP也可以是localhost
        compress: true,   // 是否配置服务器压缩
        port: 1717   // 服务运行端口号，最好不使用80, 很容易被破坏。
    }
}
```
配置之后如果直接运行"webpack-dev-server"命令会报错，不识别此命令，还需要在webpack.json里配置"script"项：
**package.json中：**
``` JavaScript
"script": {
    "server": "webpack-dev-server --open"
}
```
**--open: 设置浏览器自动打开页面**
之后终端运行命令：
``` JavaScript
npm run server
```
打包完成，浏览器就会自动打开页面。
### 如果热打包不管用，需完成以下步骤：
1. 引用webpack插件hotMoudleReplacementPlugin：
``` JavaScript
const webpack = require('webpack');
```
2. 配置插件：
``` JavaScript
module.exports = {
    entry: {},
    output: {},
    module: {},
    plugins: [
        new webpack.HotModuleReplacementPlugin()
    ],
    derServer: {}
}
```
## 打包CSS文件
<!-- webpack在生产环境中有一个重要的作就是可以减少Http请求数，就是把多个文件打包到一个Js文件里。 -->
### Loaders
loader用于对模块的源代码进行转换。loader可以使你在**import**或**加载**模块时预处理文件。因此，loader类似与其他构建工具中"任务（task）"，并提供了处理前端构建步骤的强大方法。loader可以将文件从不同的语言（如Less、Sacc、TypeScript）转换为css和JavaScript，或将内联图像转换为data URL。loader甚至允许你直接在JavaScript模块中**import**CSS文件。
**简单的使用例子：**
1. 可以把Sass的写法直接转换为CSS代码，不使用其他转换工具。
2. 可以把ES6或ES7写法转换成浏览器识别的JavaScript代码。
3. 可以把React中的JSX转换成JavaScript代码。
**所有的loader在使用之前需要使用npm进行安装，并在webpack.config.js文件中进行配置，loaders的配置项：**
* test: 用于匹配处理文件扩展名的表达式（必填）
* use: loader名称，也就是你要使用的模块名称
* include/exclude: 手动添加必须处理的文件（文件夹）或手动屏蔽不需要处理的文件（文件夹） （可选）
* query: 为loader提供额外的配置项 （可选）
### 打包css
1. 首先有.css文件，然后将此文件引入到入口文件中，如入口文件为entry.js  css文件为：index.css
``` JavaScript
import css from './css/index.css'
```
    style-loader: 用来处理CSS文件中的url()。 
    网址[https://www.npmjs.com/package/style-loader](https://www.npmjs.com/package/style-loader)
    css-loader: 用来将css插入到页面的style标签。 
    网址[https://www.npmjs.com/package/style-loader](https://www.npmjs.com/package/style-loader)
    对以上两个loader进行安装：
``` JavaScript
npm install style-loader css-loader --save-dev
```
2. loader配置
webpack.config.js
``` JavaScript
module.exports = {
    entry: {},
    output: {},
    module: {
        rules: [
            test: /\.css$/,
            use: ['style-loader', 'css-loader']
        ]
    },
    plugins: [],
    devServer: {}
}
```
### loader配置的不同写法
第一种：直接使用use
``` JavaScript
module: {
    rules: [
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
    ]
}
```
第二种： 将use换成loader
``` JavaScript
module: {
    rules: [
        test: /\.css$/,
        loader: ['style-loader', 'css-loader']
    ]
}
```
第三种： 用use+loader写法(方便添加配置参数)
``` JavaScript
module: {
    rules: [
        test: /\.css$/,
        use: [
            {
                loader: 'style-loader'，
                exclude: ''
            },
            {
                loader: 'css-loader'
            }
        ]
    ]
}
```
## 配置JS代码压缩
在上线之前，写的Js代码需要进行压缩。通过插件uglifyjs-webpack-plugin对Js进行压缩。
**因为webpack里面已经集成了uglifyjs-webpack-plugin插件，所有不需要在安装**
1. 引入插件
``` JavaScript
const uglify = require('uglifyjs-webpack-plugin');
```
2. 配置插件
``` JavaScript
module.exports = {
    entry: {},
    output: {},
    module: {},
    plugins: [
        new uglify()
    ],
    devServer: {}
}
```
## HTML文件打包
1. 首先安装插件html-webpack-plugin
``` JavaScript
npm install html-webpack-plugin --save-dev
```
2. 引入插件html-webpack-plugin
``` JavaScript
const htmlWebpackPlugin = require('html-webpack-plugin');
```
3. 配置插件
``` JavaScript
module.exports = {
    entry: {},
    output: {},
    module: {},
    plugins: [
        new htmlWebpackPlugin({
            minify: {  // 对html文件进行压缩，removeAttributeQuotes是去掉html中属性的双引号。
                removeAttributeQuotes: true
            },
            hash: true,   // 为了防止开发中有缓存，命名加入hash避免缓存
            template: './src/index.html'   // 要打包的html模板文件路径
        })
    ],
    devServer: {}
}
```

## 处理CSS中引用的图片
css文件中的图片一般以背景图片的方式引入，但是通过webpack打包之后就会出现错误。
1. 安装file-loader、url-loader
``` JavaScript
npm install file-loader url-loader --save-dev
```
    **file-loader:**处理引用路径问题，css中引用图片的路劲都是相对入口文件的路径，file-loader可以解析项目中的url引入（不限于css文件），根据我们的配置，将图片拷贝到相应的路径，在根据我们的配置，修改打包后文件引用路径
    **url-loader:**如果图片较多，会发很多http请求，会降低页面性能。这个问题可以通过url-loader解决，url-loader会将引入的图片编码，生成dataURL（base64格式）。相当于把图片编译成一串字符，在把这串字符打包到文件中，最终只需引入这个文件就可以访问图片了。如果图片较大，编程会消耗性能。url-loader提供了一个limit参数，小于limit字节的文件会被转为dataURL，大于limit字节使用file-loader进行copy。
2. 配置loader
``` JavaScript
module.exports = {
    entry: {},
    output: {},
    module: {
        rules: [
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            {
                test: /\.(png|jpg|gif)$/,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            limit: 50000     // 小于50000B将图片打包成base64格式写入js中。
                        }
                    }
                ]
            }
        ]
    },
    plugins: [],
    devServer: {}
}
```
**url-loader中封装了file-loader，所以只使用url-laoder即可。**
>参考网站：[http://jspang.com/2017/09/16/webpack3-2/](http://jspang.com/2017/09/16/webpack3-2/)

