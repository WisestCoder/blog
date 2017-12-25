### 柯里化（curry）

概念：只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数。

```javascript
var add = function(x) {
  return function(y) {
    return x + y;
  };
};

increment = add(1)(5);  // 6
```
调用 add 之后，返回的函数就通过闭包的方式记住了 add 的第一个参数。