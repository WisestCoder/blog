### 用setTimeout来模拟setInterval

```javascript
function setIntervalNew(fn, wait) {
  var timer = setTimeout(function() {
    fn();
    timer = setTimeout(arguments.callee, wait);
  }, wait);
  return timer;
}
```