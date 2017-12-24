### 延时执行函数
柯里化写法

```javascript
function fn1(str) {
  console.log(str);
}

Function.prototype.delay = function(wait) {
  var _self = this;
  return function() {
    var args = [].slice.call(arguments);
    setTimeout(function() {
      return _self.apply(this, args);
    }, wait);
  };
};

fn1('haha');
fn1.delay(5000)('haha2');
```