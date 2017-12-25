### 组合（compose）

概念：compose会把你需要函数结合在一起，像一根管道一样，函数就是这跟管道的节点。你只需要从管道的开始端注入数据，管道会把你的数据处理成你想要的数据然后返回给你。

```javascript
var compose = function(f , g){
    return function(x){
        return f(g(x));
    };
};
```

在 compose 的定义中，g 将先于 f 执行，因此就创建了一个从右到左的数据流。

**结合律**
```javascript
// 结合律（associativity）
var associative = compose(f, compose(g, h)) == compose(compose(f, g), h);
// true
```

**pointfree**
pointfree 模式指的是，不必说出你的数据是什么。
```javascript
// 非 pointfree，因为提到了数据：word
var snakeCase = function (word) {
  return word.toLowerCase().replace(/\s+/ig, '_');
};

// pointfree
var snakeCase = compose(replace(/\s+/ig, '_'), toLowerCase);
```
pointfree 模式能够帮助我们减少不必要的命名，让代码保持简洁和通用。

**compose实现**
下面是compose函数的实现代码。思路就是先把传入的函数都缓存起来，然后在传入数据的时候，再挨个的使用apply执行函数， 上一个函数的输出数据，作为下一个函数的输入数据。

compose遵循的是从右向左运行，而不是由内而外运行。也就是说compose是从最后一个函数开始执行。
```javascript
var compose = function() {
  var args = arguments;
  var start = args.length - 1;
  return function() {
    var i = start;
    var result = args[start].apply(this, arguments);
    while (i--) result = args[i].call(this, result);
    return result;
  };
};
```
