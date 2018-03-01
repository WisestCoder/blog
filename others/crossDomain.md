### 跨域详解

#### 1. document.domain
document.domain这跨域个方法主要适用于 ajax跨子域，比如在 a.takozhang.cn 中使用ajax调用 takozhang.cn 中的接口时。

1. 在被访问接口的服务器中，新建一个proxy.html文件，里面的代码是：
```javascript
<script>
  document.domain = 'takozhang.cn'
</script>
<script src = 'jquery.js'></script>
```

2. 在调用页面使用一个隐藏的iframe，地址指向proxy.html，代码如下：
```javascript
<iframe src="http://takozhang.cn/proxy.html" id="proxy"></iframe>
```

3. 在a.takozhang.cn服务器中进行ajax发送请求的时候，代码如下：
```javascript
var iframe = document.getElementById('proxy').contentWindow;
iframe.window.JQuery.ajax({
  url: 'takozhang.cn/php/index.php',
  method: 'post',
  success: function(data){},
  error: function(err){}
});
```

#### 2. window.name
window.name 只用于两个完全不同源的域，比如www.a.com   www.b.com

window.name属性有个特征：即在一个窗口(window)的生命周期内,窗口载入的所有的页面都是共享一个window.name

1. 在www.a.com的服务器中创建一个iframe，并引入www.b.com，代码如下：
```javascript
<script>
  var inf=document.createElement("iframe");       //创建iframe
  inf.src="http://www.b.com/index.html"+"?h=5"  //加载数据页www.b.com/index.html的同时携带参数h=5
  var body=document.getElementsByTagName("body")[0];
  body.appendChild(inf);                          //引入iframe

  inf.onload=function(){
      inf.src='http://www.a.com/index.html'       //iframe加载完成，加载www.a.com域下边的空白页index.html
      console.log(inf.contentWindow.name)        //输出window.name中的数据
      body.removeChild(inf)                      //清除iframe
  }
</script>
```

2. 在www.b.com中的index.html中进行ajax，并把返回值通过window.name储存，代码如下：
```javascript
  var str=window.location.href.substr(-1,1);      //获取url中携带的参数值
  $.ajax({
      type:"post",
      url:"http://www.b.com/index.php"+"?m="+str, //通过ajax将查询参数传给php页面
      success:function(res){
          window.name=res                         //将接收到的查询数据赋值给window.name
      },
      error:function(){
          window.name='error'
      }
  });
```

整体过程就是：a服务器的页面中中创建一个iframe，iframe引入b服务器中进行ajax的页面，该页面ajax之后将返回值赋值给window.name，a服务器页面监听iframe事件的加载完成，再将iframe地址指向a服务器中的某个空白页面，然后读取iframe的window.name。

#### 3. window.postMessage
`window.postMessage(message,targetOrigin)` 方法是html5新引进的特性，可以使用它来向其它的window对象发送消息，
无论这个window对象是属于同源或不同源，目前IE8+、FireFox、Chrome、Opera等浏览器都已经支持window.postMessage方法。

在父页面中嵌入子页面，通过postMessage发送数据。parent.com/index.html中的代码：
```javascript
<iframe id="ifr" src="child.com/index.html"></iframe>
<script type="text/javascript">
window.onload = function() {
    var ifr = document.getElementById('ifr');
    var targetOrigin = 'http://child.com';
    // 若写成'http://child.com/c/proxy.html'效果一样
    // 若写成'http://c.com'就不会执行postMessage了
    ifr.contentWindow.postMessage('I was there!', targetOrigin);
};
</script>
```

在子页面中通过message事件监听父页面发送来的消息并显示。`child.com/index.html中的代码：
```javascript
<script type="text/javascript">
window.addEventListener('message', function(event){
    // 通过origin属性判断消息来源地址
    if (event.origin == 'http://parent.com') {
        alert(event.data);    // 弹出"I was there!"
        alert(event.source);
        // 对parent.com、index.html中window对象的引用
        // 但由于同源策略，这里event.source不可以访问window对象
    }
}, false);
</script>
```

主要是用来传递信息，父页面使用`window.postMessage()`,子页面使用`window.addEventListener('message',function(event){})`

#### 4. jsonp

基本可以适用于任何场景

1. 客户端动态创建script标签，并利用src属性去向服务端获取数据 src="www.a.com/index.php?callback=fn",切记一定要在后面加上一个callback回调函数
2. 服务端会将请求的json数据放在这个你指定的回调函数里作为参数返回回来，如服务器会返回 fn(data),
3. 客户端写好回调函数的实现，处理数据。

#### 5. cors
对于前端开发者来说，跨域的CORS通信与同源的AJAX通信没有差别，代码完全一样。因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。

