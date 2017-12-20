### before和after函数

#### 使用场景
在执行一个函数前或者是执行某个函数后需要做一些事情

#### 实现原理
主要用到函数的aplly调用

#### 代码实现
`before函数`
```javascript
Function.prototype.before = function(callback) {
  var _self = this;
  return function() {
    callback.apply(this, arguments);
    return _self.apply(this, arguments);
  }
}

var ret = fn1.before(fn2)(5);
console.log(ret);
```

`after函数`
```javascript
Function.prototype.after = function(callback) {
  var _self = this;
  var result;
  return function() {
    result = _self.apply(this, arguments);
    callback.apply(this, arguments);
    return result;
  }
}

var ret = fn2.after(fn1)(5);
console.log(ret);
```