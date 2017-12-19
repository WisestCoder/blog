### 数据类型的判断

#### typeof
对一个值使用`typeof`操作符可能返回下列某个字符串：
```javascript
"undefined" —— 如果这个值未定义；
"boolean" —— 如果这个值是布尔值；
"string" —— 如果这个值是字符串；
"number" —— 如果这个值是数值；
"object" —— 如果这个值是对象或 null；
"function" —— 如果这个值是函数。
```
注意：
 - `typeof`是一个操作符而不是函数，所以`typeof`后面的圆括号可加可不加。
 - 调用`typeof null`会返回 "object"，因为特殊值`null`被认为是一个空的对象引用。Safari 5 及之前版本、Chrome 7 及之前版本在对正则表达式调用`typeof`操作符时会返回 "function"，而其他浏览器在这种情况下会返回 "object"。
 - 我们无法区分对象，数组和`null`，因为这三者的返回值都是 "object"。

 #### instanceof
其语法如下所示：
```javascript
result = variable instanceof constructor
exp: console.log(person instanceof Object)  // 变量person是Object 吗？
```
注意：
 - `instanceof`只能检测引用类型，所以在判断基本类型的值时始终会返回 false，例如`1 instanceof Number => false`。

 #### Object.prototype.toString.call
 `Object.prototype.toString.call`是最安全可靠的检测方式，因为我们调用的是Object上的原生的toString()方法。
 
 其实js 里面还有好多类型判断，如[object HTMLDivElement] == div 对象、[object HTMLBodyElement] == body 对象、还有`[object Document](IE)`或者`[object HTMLDocument](firefox,google)` ...各种dom节点的判断，这些东西在我们写插件的时候都会用到。

 我们可以做以下的类型判断封装（前提是`Object.prototpye.toString`是未被修改过的原生版本）：
```javascript
var gettype = Object.prototype.toString
var  util = {
    isObj: function(o) {
        return  gettype.call(o)=="[object Object]";
    },
    isArray: function(o) {
        return  gettype.call(o)=="[object Array]";
    },
    isNull:function(o) {
        return  gettype.call(o)=="[object Null]";
    },
    isDocument: function(o) {
        return  gettype.call(o)=="[object Document]"|| [object HTMLDocument];
    },
    isFunction: function (o) { 
        return Object.prototype.toString.call(0) == "[object Function]"; 
    },
    isRegExp: function(o) { 
        return Object.prototype.toString.call(0) == "[object RegExp]"; 
    },
    isJson: function(o) {
        return !!(window.JSON && Object.prototype.toString.call(JSON) == "[object JSON]";);
    },
    ........
}
```

[参考链接](https://www.cnblogs.com/dushao/p/5999563.html)
