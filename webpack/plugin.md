## 本文将介绍如何开发一个webpack插件

插件向第三方开发者提供了 webpack 引擎中完整的能力。使用阶段式的构建回调，开发者可以引入它们自己的行为到 webpack 构建流程中。

### Compiler 和 Compilation
开发插件最重要的两个资源就是 `compiler` 和 `compilation` 对象，理解他们的是扩展Webpack重要的一步
- `compiler`对象包涵了Webpack环境所有的的配置信息，这个对象在Webpack启动时候被构建，并配置上所有的设置选项包括 options，loaders，plugins。当启用一个插件到Webpack环境的时候，这个插件就会接受一个指向compiler的参数。运用这个参数来获取到Webpack环境
- `compilation`代表了一个单一构建版本的物料。在webpack中间件运行时，每当一个文件发生改变时就会产生一个新的compilation从而产生一个新的变异后的物料集合。compilation列出了很多关于当前模块资源的信息，编译后的资源信息，改动过的文件，以及监听过的依赖。compilation也提供了插件需要自定义功能的回调点。

这两个组件在所有的Webpack插件中都是不可分割的一部分（特别是`compilation`），所以对于开发者来说熟悉这两个组件的源文件将是你受益很多：

- [Compiler 源文件](https://github.com/webpack/webpack/blob/master/lib/Compiler.js)
- [Compilation 源文件](https://github.com/webpack/webpack/blob/master/lib/Compilation.js)

### 插件基本结构

Plugins是可以用自身原型方法`apply`来实例化的对象。`apply`只在安装插件被Webpack compiler执行一次。`apply`方法传入一个Webpck compiler的引用，来访问编译器回调。

一个简单的插件结构：

```javascript
function HelloWorldPlugin(options) {
  // Setup the plugin instance with options...
}

HelloWorldPlugin.prototype.apply = function(compiler) {
  compiler.plugin('done', function() {
    console.log('Hello World!'); 
  });
};

module.exports = HelloWorldPlugin;
```

安装插件时, 只需要将它的一个实例放到 Webpack config `plugins` 数组里面:

```javascript
var HelloWorldPlugin = require('hello-world');

var webpackConfig = {
  // ... config settings here ...
  plugins: [
    new HelloWorldPlugin({options: true})
  ]
};
```

### 访问 compilation

使用compiler对象，你可能需要绑定带有各个新compilation的引用的回调函数。这些compilation提供回调函数连接成许多构建过程中的步骤。

```javascript
function HelloCompilationPlugin(options) {}

HelloCompilationPlugin.prototype.apply = function(compiler) {

  // Setup callback for accessing a compilation:
  compiler.plugin("compilation", function(compilation) {
    
    // Now setup callbacks for accessing compilation steps:
    compilation.plugin("optimize", function() {
      console.log("Assets are being optimized.");
    });
  });
});

module.exports = HelloCompilationPlugin;
```

更多关于在`compiler`, `compilation`等对象中哪些回调有用，后面的[插件 API](#pluginApi)会讲到。

### 异步编译插件
有些compilation插件的步骤时异步的，并且会传入一个当你的插件运行完成时候_必须_调用的回调函数。

```javascript
function HelloAsyncPlugin(options) {}

HelloAsyncPlugin.prototype.apply = function(compiler) {
  compiler.plugin("emit", function(compilation, callback) {

    // Do something async...
    setTimeout(function() {
      console.log("Done with async work...");
      callback();
    }, 1000);

  });
});

module.exports = HelloAsyncPlugin;
```

### 例子

我们了解了Webpack compiler和各个compilations，我们就可以用它们来创造无尽的可能。我们可以重定当前文件的格式，生成一个衍生文件，或者制造出一个全新的assets

下面我们将写一个简单的插件，生成一个`filelist.md`文件，里面的内容是，列出我们build的所有asset 文件。

```javascript
function FileListPlugin(options) {}

FileListPlugin.prototype.apply = function(compiler) {
  compiler.plugin('emit', function(compilation, callback) {
    // Create a header string for the generated file:
    var filelist = 'In this build:\n\n';

    // Loop through all compiled assets,
    // adding a new line item for each filename.
    for (var filename in compilation.assets) {
      filelist += ('- '+ filename +'\n');
    }
    
    // Insert this list into the Webpack build as a new file asset:
    compilation.assets['filelist.md'] = {
      source: function() {
        return filelist;
      },
      size: function() {
        return filelist.length;
      }
    };

    callback();
  });
};

module.exports = FileListPlugin;
```

### <span id="pluginApi">插件 API</span>

很多 webpack 中的对象都继承了 Tapable 类，暴露了一个 plugin 方法。插件可以使用 plugin 方法注入自定义的构建步骤。你可以看到 compiler.plugin 和 compilation.plugin 被频繁使用。基本上，每个插件的调用都在构建流程中绑定了回调来触发特定的步骤。

每个插件会在 webpack 启动时被安装一次，webpack 通过调用插件的 apply 方法来安装它们，并且传递一个 webpack compiler 对象的引用。然后你可以调用 compiler.plugin 来访问资源的编译和它们独立的构建步骤。下面就是一个示例：
```javascript
// MyPlugin.js

function MyPlugin(options) {
  // 根据 options 配置你的插件
}

MyPlugin.prototype.apply = function(compiler) {
  compiler.plugin("compile", function(params) {
    console.log("The compiler is starting to compile...");
  });

  compiler.plugin("compilation", function(compilation) {
    console.log("The compiler is starting a new compilation...");

    compilation.plugin("optimize", function() {
      console.log("The compilation is starting to optimize files...");
    });
  });

  compiler.plugin("emit", function(compilation, callback) {
    console.log("The compilation is going to emit files...");
    callback();
  });
};

module.exports = MyPlugin;
```
然后在`webpack.config.js`中配置：
```javascript
plugins: [
  new MyPlugin({options: 'nada'})
]
```

[点击查看完整的查看API介绍](http://www.css88.com/doc/webpack2/api/plugins/#%E6%8F%92%E4%BB%B6%20API)

[插件模式](http://www.css88.com/doc/webpack2/development/plugin-patterns/)

[Tapable](https://doc.webpack-china.org/api/tapable/)

[Tapable 和 事件流](https://segmentfault.com/a/1190000008060440)

[整体调用流程与Tapable事件流](https://blog.csdn.net/qiqingjin/article/details/71092660)
