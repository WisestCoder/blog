## 怎样写一个webpack loader

有什么样的场景需要写一个loader呢？

loader加载器就是对各种非正常资源的解析，转化成浏览器可以识别的js/css文件等，甚至可以说loader就是一个小型的编译器。像我们写react项目，使用的是jsx语法， jsx语法不被浏览器支持，因此我们需要去编译，loader就是做这个事的，再比如我们写样式，可能使用到预编译语法sass、less啥的，也需要编译成css。

回到正题，怎么写一个loader

### loader的基本代码模型

所谓 Loader 只是导出了一个函数的 JavaScript 模块。Compiler 会调用这个函数，然后把之前 Loader 产生的结果或者资源文件传入进去。这个函数中的 this 里会有一些 Compiler 添加的很有用的方法，比如可以让 Loader 的调用方式变成异步的，或者得到一些 query 参数。第一个 Loader 传入的参数只有一个：资源文件的内容。Complier 会接收上一个 Loader 产生的处理结果。这些处理结果应该是一些 String 或者 Buffer（被转换为一个 string），代表模块的 JavaScript 源码。另外可选的 SourceMap 信息（作为一个 JSON 对象）可能也会被传入。

```javascript
module.exports = function(source,map) {
  //对source进行解析
  var exports = process(source);
  return 'module.exports = ' + exports;
}
```

是的，你没看错，一个loader就是这么简单。关键在于理解loader的机制——其实loader最后会创建一个模块，当我们require一个需要让loader的解析的文件之后，会调用上面的方法，然后require一个被loader处理过后的模块。

我们可以看一眼[json-loader](https://github.com/webpack-contrib/json-loader/blob/master/index.js)的源码

```javascript
module.exports = function (source) {
  if (this.cacheable) this.cacheable();

  var value = typeof source === "string" ? JSON.parse(source) : source;

  value = JSON.stringify(value)
    .replace(/\u2028/g, '\\u2028')
    .replace(/\u2029/g, '\\u2029');

  return `module.exports = ${value}`;
}
```

### loader的一些特性

#### 1. loader支持链式
loader支持链式，上一个loader的处理结果可以给下一个loader，像sass的loader是这样写的：
```javascript
{
  test:/\.scss$/,
  loaders:["style","css","sass"]
}
```
sass处理完之后给css的loader，css的loader处理完后给style的loader，loader间的数据传递通过loader的定义的callback函数，例如上面的loader可以在return之前再加一行：
```javascript
this.callback(null,source,map);
```
#### 2. loader可缓存，可以加快速度
```javascript
this.cacheable(flag = true: boolean)
```
默认情况下，Loader 的处理结果是会被缓存的。调用这个方法然后传入 false，可以关闭 Loader 的缓存。

一个可缓存的 Loader 要求在输入和相关依赖没有变化时，绝对产生一个确定性的固定处理结果。目前大多数 Loader 都是确定性、可缓存的。

### 3. loader支持异步
```javascript
module.exports = function(content) {
    var callback = this.async();
    if(!callback) return someSyncOperation(content);
    someAsyncOperation(content, function(err, result) {
        if(err) return callback(err);
        callback(null, result);
    });
};
```

官方建议，异步 Loader 最好能有一个同步模式的降级方案。这虽然不是 Webpack 的要求，但这可以让 Loader 同步运行时使用 [enhanced-require](https://github.com/webpack/enhanced-require).

最后我们在看一眼jsx-loader的代码
```javascript
var jstransform = require('jstransform/simple');
var loaderUtils = require('loader-utils');

module.exports = function(source) {
  this.cacheable && this.cacheable();

  var sourceFilename = loaderUtils.getRemainingRequest(this);
  var current = loaderUtils.getCurrentRequest(this);

  var query = loaderUtils.parseQuery(this.query);
  if (query.insertPragma) {
    source = '/** @jsx ' + query.insertPragma + ' */' + source;
  }

  var transform = jstransform.transform(source, {
    react: true,
    harmony: query.harmony,
    stripTypes: query.stripTypes,
    es5: query.es5,
    sourceMap: this.sourceMap
  });
  if (transform.sourceMap) {
    transform.sourceMap.sources = [sourceFilename];
    transform.sourceMap.file = current;
    transform.sourceMap.sourcesContent = [source];
  }
  this.callback(null, transform.code, transform.sourceMap);
};
```