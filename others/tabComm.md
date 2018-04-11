### 如何实现不同标签页之间的通信

1. 使用localStorage

```javascript
window.addEventListener("storage",function(event) {  
  ...
});  
```

2. cookie + setInterval

```javascript
// 页面1修改cookie
document.cookie = 'name=' + name;  
```

```javascript
// 页面2每隔1s读取cookie
setInterval(function() {  
  // 取到页面1埋下的cookie值
 }, 1000);  
```

3. sharedworker

共享进程可以连接到多个不同的同域页面，从而进行多个页面之间的通信。

[demo地址](https://github.com/wisestcoder/sharedworker-demo)