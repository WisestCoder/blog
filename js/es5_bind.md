### ES5中的bind实现方法

**使用call实现**
```javascript
Function.prototype.bind = function(oThis) {
  if (typeof this !== 'function') {
    // closest thing possible to the ECMAScript 5
    // internal IsCallable function
    throw new TypeError('Function.prototype.bind - what is trying to be bound is not callable');
  }

  var aArgs   = Array.prototype.slice.call(arguments, 1),
      fToBind = this,
      fNOP    = function() {},
      fBound  = function() {
        return fToBind.apply(this instanceof fNOP
              ? this
              : oThis,
              // 获取调用时(fBound)的传参.bind 返回的函数入参往往是这么传递的
              aArgs.concat(Array.prototype.slice.call(arguments)));
      };

  // 维护原型关系
  if (this.prototype) {
    // Function.prototype doesn't have a prototype property
    fNOP.prototype = this.prototype; 
  }
  fBound.prototype = new fNOP();

  return fBound;
};
```

**不使用apply和call实现**
[不用call和apply方法模拟实现ES5的bind方法](https://github.com/jawil/blog/issues/16)