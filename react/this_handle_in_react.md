### 5种处理this的方法

1、use React.createClass

当使用 React.createClass 来定义组件时，React 允许你随意在此组件的类中定义方法，而在方法中调用的 this 会自动绑定到组件自身：
```javascript
React.createClass({
  ...
  // This magically works with React.createClass
  // because `this` is bound for you.
  onChange={this.handleChange}
  ...
});
```
对于不是非常复杂的组件来说，这是一种非常不错的解决 this 指向问题的方式。而事实上呢，如果在组件的方法中使用`.bind(this)`，React 会抛出一个警告：
> bind(): You are binding a component method to the component. React does this for you automatically in a high-performance way, so you can safely remove this call.

但对于`ES6`的类来说，自动绑定并不适用。

2、bind in render

这种方式是在函数运行时将 this 注入到回调中，使回调中的 this 能指向正确的上下文：
```javascript
onChange={this.handleChange.bind(this)}
```
在 JavaScript 中，所有函数都有 bind 方法，其允许你为 this 指定特定值。一旦函数被绑定，上下文就不能被覆盖，也就意味着 this 会指向正确的上下文。

3、Use Arrow Function in Render

ES2015 规范引入了箭头函数，使函数的定义更加简洁。箭头函数会隐式返回一个值，但更重要的是，它是在一个封闭的作用域中使用 this：
```javascript
onChange={e => this.handleChange(e)}
```
不管嵌套多少层，箭头函数中的 this 总能指向正确的上下文，因为函数体内的 this 指向的对象，就是定义时所在的对象，而不是使用时所在的对象。但缺点就是，由于箭头函数不能命名，因而在调试时，堆栈信息给的标签是`anonymous function`。

4、this rename

这种方式就是在 React 组件的作用域顶端创建一个指向 this 的变量：
```javascript
var _this = this;
onChange={function() {
  _this.handleChange(e)
}}
```

5、ES7

在 ES7 中，有一个关于 bind 语法 的提议，提议将 :: 作为一个新的绑定操作符，该操作符会将左值和右值(一个函数)进行绑定。
```javascript
onChange={this::function() {
  _this.handleChange(e)
}}
```