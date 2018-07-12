## 面试题

**$.ajax返回的是什么？**
jQuery 1.4 中传递的是 XMLHttpRequest 对象。

对于 jQuery 1.5及以上，所有 jQuery 的 AJAX 方法返回的是 XMLHTTPRequest 对象的超集，既[jqXHR对象](http://www.w3school.com.cn/jquery/ajax_post.asp#jqxhr_object)。由`$.post()`返回的 jQuery XHR 对象或 "jqXHR,"实现了约定的接口，赋予其所有的属性、方法，以及约定的行为。出于对由`$.ajax()`使用的回调函数名称便利性和一致性的考虑，它提供了 .error(), .success() 以及 .complete() 方法。这些方法使用请求终止时调用的函数参数，该函数接受与对应命名的 $.ajax() 回调函数相同的参数。

```javascript
// 请求生成后立即分配处理程序，请记住该请求针对 jqxhr 对象
var jqxhr = $.post("example.php", function() {
  alert("success");
})
.success(function() { alert("second success"); })
.error(function() { alert("error"); })
.complete(function() { alert("complete"); });

// 在这里执行其他任务

// 为上面的请求设置另一个完成函数
jqxhr.complete(function(){ alert("second complete"); });
```

**事件绑定**

js事件绑定有三种方法

1. 在DOM元素中直接绑定
  ```javascript
  <input  onclick="myAlert()"  type="button"  value="点击我，弹出警告框" />
  <script type="text/javascript">
  function myAlert(){
      alert("谢谢支持");
  }
  </script>
  ```
2. 在JavaScript代码中绑定
  ```javascript
  <input  id="demo"  type="button"  value="点击我，显示 type 属性" />
  <script type="text/javascript">
  document.getElementById("demo").onclick=function(){
      alert(this.getAttribute("type"));  //  this 指当前发生事件的HTML元素，这里是<div>标签
  }
  </script>
  ```
3. 