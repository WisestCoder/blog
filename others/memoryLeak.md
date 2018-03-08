### 内存泄露

什么是内训泄露？[点击查看](http://www.ruanyifeng.com/blog/2017/04/memory-leak.html)

#### 造成内存泄露的原因

1. 全局变量引起的内存泄漏

```javascript
function leaks(){  
  leak = 'xxxxxx'; // leak 成为一个全局变量，不会被回收
}
```

2. 闭包引起的内存泄漏

```javascript
var leaks = (function() {  
  var leak = 'xxxxxx'; // 被闭包所引用，不会被回收
  return function() {
    console.log(leak);
  }
})()
```

3. js机制导致创建了一些临时对象
```javascript
var str = 'abc';
console.log(str.length); // str默认是没有长度的，js会默认创建一个new String(str)的临时对象
```

4. dom清空或删除时，事件未清除导致的内存泄漏
```javascript
<div id="container"></div>

$('#container').bind('click', function(){
  console.log('click');
}).remove();
```

5. 对象之间的相互引用
```javascript
var a = document.getElementById("#xx"); 
var b = document.getElementById("#xxx"); 
a.r = b; 
b.r = a; 
// 在 Internet Explorer 中，如果循环引用中的任何对象是 DOM 节点或者 ActiveX 对象，垃圾收集系统则不会发现它们之间的循环关系与系统中的其他对象是隔离的并释放它们。最终它们将被保留在内存中，直到浏览器关闭。
```