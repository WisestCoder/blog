### UMD规范
UMD主要是为了兼容AMD和commonjs。

UMD的实现很简单，先判断是否支持AMD规范(define是否存在）；再判断是否支持commonjs（exports是否存在）；前两个都不存在，则将模块公开到全局（window或global）。

```javascript
// if the module has no dependencies, the above pattern can be simplified to
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD. Register as an anonymous module.
        define([], factory);
    } else if (typeof exports === 'object') {
        // Node. Does not work with strict CommonJS, but
        // only CommonJS-like environments that support module.exports,
        // like Node.
        module.exports = factory();
    } else {
        // Browser globals (root is window)
        // node globals (root is global)
        root.returnExports = factory();
  }
}(this, function () {

    // Just return a value to define the module export.
    // This example returns an object, but the module
    // can return a function as the exported value.
    return {};
}));
```

