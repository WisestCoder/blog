## webpack知识点详解

### 前言
最近有个小伙伴突然问我说她看了webpack很久，但总缺少对webpack深入的认识；我仔细想了想，其实自己用webpack好几年了，很多东西自己也没怎么好好梳理，趁着这次机会，打算把webpack从头到尾、由简至繁分析一遍。

### 为什要使用WebPack

现今的很多网页其实可以看做是功能丰富的应用，它们拥有着复杂的JavaScript代码和一大堆依赖包。为了简化开发的复杂度，前端社区涌现出了很多好的实践方法。

- 模块化，让我们可以把复杂的程序细化为小的文件
- 类似于TypeScript这种在JavaScript基础上拓展的开发语言：使我们能够实现目前版本的JavaScript不能直接使用的特性，并且之后还能转换为JavaScript文件使浏览器可以识别
- ES6、ES7、ES8等ECMAScript新规范语法，需要支持一下低版本浏览器
- Scss，less等CSS预处理器
- ...

这些新的语法、新的改进确实大大的提高了我们的开发效率，但是利用它们开发的文件往往需要进行额外的处理才能让浏览器识别,而手动处理又是非常繁琐的，这就为WebPack类的工具的出现提供了需求。

### 什么是Webpack

WebPack可以看做是模块化打包机制：它做的事情是，分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其转换和打包为合适的格式供浏览器使用。

换句话说：Webpack 是一个前端资源加载/打包工具，只需要相对简单的配置就可以提供前端工程化需要的各种功能。

### WebPack和Grunt以及Gulp相比有什么特性
其实Webpack和另外两个并没有太多的可比性，Gulp/Grunt是一种能够优化前端的开发流程的工具，而WebPack是一种模块化的解决方案，不过Webpack的优点使得Webpack在很多场景下可以替代Gulp/Grunt类的工具。

Grunt和Gulp的工作方式是：在一个配置文件中，指明对某些文件进行类似编译，组合，压缩等任务的具体步骤，工具之后可以自动替你完成这些任务。

![](https://github.com/wisestcoder/blog/blob/master/webpack/images/gulp_flow.png)

Webpack的工作方式是：把你的项目当做一个整体，通过一个给定的主文件（如：index.js），Webpack将从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包为一个（或多个）浏览器可识别的JavaScript文件。

前端工程看[这里](https://github.com/fouber/blog/issues/10)

![](https://github.com/wisestcoder/blog/blob/master/webpack/images/webpack_flow.png)

### 怎么使用webpack
webpack具体每个配置项我就不一一列举，想完全了解的可以去看看官网，或者是[中文站点](https://webpack.docschina.org/concepts/)

此处我主要分析webpack在一个前端项目到底能做什么？做了什么？

1. 依赖加载
  不管我们是使用CommonJS、还是AMD，又或者是es6的模块化，只要我们指定好入口和出口，webpack都能顺着依赖关系去模块化处理我们的代码
2. 编译代码
  框架流行的时代，一个新的项目，我们通常会使用react、vue或者angular+ts去开发，像react的jsx语法，vue的模板等等，又或者是ts、es6+、scss/less等等，这些语法是浏览器并不支持的，webpack提供的一些loader能够处理我们的代码
3. 开发调试
  webpack能够启动本地调试服务，并且提供了热更新的插件，让我们能快速地在本地调试我们的代码
4. 代码压缩
5. 按需加载
6. 抽离公共模块

[webpack实战](https://segmentfault.com/a/1190000015020658)

### webpack各个版本的差异

#### webpack从v1迁移至v2

1.配置方式不同

在webpack1的时候，主要是通过导出单个object来进行配置。例如下面的配置：
```javascript
// webpack1 导出方式
module.export = {
    entry : 'app.js',
    output : { */... */},
    /* ... */
};
```

webpack2的配置添加了env环境
```javascript
// 可以有两种方式传递当前值，一种是简单传递字符串，另外一种则是传递一个对象
// 例如： webpack --env production 控制台打印的就是 'production'，是一个字符串
// 而当这样调用时：webpack --env.production --env.size 60，控制台打印的就是 { production : true, size : 60 }
// 可以通过判断env来对不同环境做不同配置
module.exports = function(env) {
    console.log(env);
    ...
}
```

2.module.rules替换module.loaders

在v2中，如果需要加载的模块只需要一个loader，那么你还是可以直接用loader这个关键词；如果要加载的模块需要多个loader，那么你需要使用use这个关键词，在每个loader中都可以配置参数。代码如下：
```javascript
module : {
    rules : [
        { test : /\.js|\.jsx$/, loader : 'babel-loader' },

        /* 如果后面有参数需要传递到当前的loader，则在后面继续加上options关键词，例如：
          { 
            test : /\.js|\.jsx$/, 
            loader : 'babel-loader', 
            options : { presets : [ 'es2015', 'react' ] } 
          }
        */

        {
            test : /\.css$/,
            // webpack1 way
            // loader : 'style!css'

            use : [ 'style-loader', 'css-loader' ]
        },
        {
            test : /\.less$/,
            use : [
                'style-loader',     // 默认相当于 { loader : 'style-loader' }
                {
                    loader : 'css-loader',
                    options : {
                        modules : true
                    }
                },
                'less-loader'
            ]
        }
    ]
}
```

3.取消自动添加-loader后缀

之前写loader通常是这样的：
```javascript
loader : 'style-loader!css-loader!less-loader'
```

v2是这样写：
```javascript
loader : 'style!css!less'
```

4.json-loader内置

如果要加载json文件的童鞋再也不需要配置json-loader了，因为webpack2已经内置了

5.UglifyJsPlugin代码压缩插件

压缩插件中的warnings和sourceMap不再默认为true，如果要开启，可以这样配置：
```javascript
plugins : [
    new UglifyJsPlugin({
        souceMap : true,
        warnings : true
    })
]
```

6.ExtractTextWebapckPlugin插件

主要是写法上的变动，要和webpack2配合使用的话，需要使用version 2版本

```javascript
// webpack1 way
modules : {
    loaders : [
        { 
            test : /\.css$/, 
            loader : ExtractTextPlugin.extract('style-loader', 'css-loader', { publicPath : '/dist' })
        }   
    ]
},
plugins : [
    new ExtractTextPlugin('bunlde.css', { allChunks : true, disable : false })
]

// webapck2 way
modules : {
    rules : [
        { 
            test : /\.css$/, 
            use : ExtractTextPlugin.extract({
                fallback : 'style-loader',
                use : 'css-loader',
                publicPath : '/dist'
            })
        }
    ]
},
plugins : [
    new ExtractTextPlugin({
        filename : 'bundle.css',
        disable : false,
        allChunks : true
    })
]
```

7.loaders的debug模式

在webpack1中要开启loaders的调试模式，需要加载debug选项，在webpack2中不再使用，在webpack3或者之后会被删除。如果你想继续使用，那么请使用以下写法：

```javascript
// webpack1 way
debug : true

// webapck2 way 
// webapck2将loader调试移到了一个插件中
plugins : [
    new webpack.LoaderOptionsPlugin({
        debug : true
    })
]
```

8.增加了tree-shaking

不懂的可以戳[这里](https://github.com/wisestcoder/blog/blob/master/webpack/treeShaking.md)了解一下

#### webpack从v2迁移至v3

两个版本几乎完全兼容，新增部分新特性。

1.加入 Scope Hoisting（作用域提升）
- 之前版本将每个依赖都分别封装在一个闭包函数中来独立作用域。这些包装函数闭包函数降低了浏览器 JS 引擎解析速度
- Webpack 团队参考 Closure Compiler 和 Rollup JS，将有联系的模块放到同一闭包函数中，从而减少闭包函数数量，使文件大小的少量精简，提高 JS 执行效率
- 在 Webpack3 配置中加入 ModuleConcatenationPlugin 插件来启用作用域提升

```javascript
module.exports = { plugins: [ new webpack.optimize.ModuleConcatenationPlugin() ] };
```

2.加入 Magic Comments（魔法注解）

在 Webpack2 中引入了 Code Splitting-Async 的新方法 import()，用于动态引入 ES Module，Webpack 将传入 import 方法的模块打包到一个单独的代码块（chunk），但是却不能像 require.ensure 一样，为生成的 chunk 指定 chunkName。因此在 Webpack3 中提出了 Magic Comment 用于解决该问题
```javascript
import(/* webpackChunkName: "my-chunk-name" */ 'module');
```

#### webpack4相较于其他版本的改动

1.安装

不在只安装webpack即可，还需要安装一个webpack-cli：

全局安装
```bash
sudo npm install -g webpack webpack-cli
```

局部安装（当前文件夹）
```bash
npm install --save-dev webpack webpack-cli
```

2.配置

默认的入口为'./src/'和默认出口'./dist'

对压缩(optimization.minimize)的设置，默认在production时开启，在development时关闭。

3.mode/–mode参数

新增了mode/--mode参数来表示是开发还是生产，mode有两个可选值：development和production，production不支持监听，production侧重于打包后的文件大小，development侧重于构建的速度。
```bash
webpack --mode development
```

也可以在配置文件中配置：
```javascript
// webpack.config.js
module.exports = {
    mode: "production",
    // ...
}
```

4.对uglifyjs升级

在之前的vue脚手架创建的基于webpack的工程，在webpack.prod.conf.js中可以清晰的看到配置中有这么一行：

>UglifyJs do not support ES6+, you can also use babel-minify for better treeshaking: https://github.com/babel/minify

5.移除loaders，必须使用rules

在webpack3.x中还保留之前版本的loaders，与rules并存都可以使用，在新版中完全移除了loaders，必须使用rules。

6.sideEffects

在模块的package.json中添加sideEffects:false，当使用export单独导出的时候，不会打包export之外的其他文件，使打包的文件更小。

[sideEffects官方解释](https://github.com/webpack/webpack/tree/master/examples/side-effects)

7.webpack4支持多种形式的模块类型，但是有时候可能需要加上type进行配合

- 如果是CommonJS, AMD, ESM三种类型的模块，使用javascript/auto； 
- 如果是EcmaScript modules(.mjs)，使用javascript/esm，其他的模块类型将不生效； 
- 如果只有CommonJS和EcmaScript modules不可用，使用javascript/dynamic； 
- 如果是json类型的文件，允许使用require和import来导入json，使用json； 
- 如果是Webassembly，使用webassembly/experimental —— 官方说是一个实验性的功能。

举个栗子：
```javascript
rules: [
   {
        test: /\.json$/,
        type: "javascript/auto"
    }
]
```

8.支持ES6的方式导入JSON文件，并且可以过滤无用的代码

下面是三种导入json文件的方法：
```javascript
let jsonData = require('./data.json');

import jsonData from './data.json'

import { first } from './data.json'
```

>其中使用import { first } from './data.json'引入的json文件会忽略没导入的代码，打包时只会将first的打进去。

9.移除CommonsChunkPlugin，用optimization.splitChunks和optimization.runtimeChunk来代替


### webpack进阶

[代码分片/按需加载](https://blog.csdn.net/beijiyang999/article/details/78591398)

[webpack性能优化](https://github.com/WisestCoder/blog/issues/2)

[webpack构建性能优化策略小结](https://segmentfault.com/a/1190000007891318)

[开发工具心得：如何10倍提高你的Webpack构建效率](https://segmentfault.com/a/1190000005770042)

