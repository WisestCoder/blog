### babel-polyfill

Babel默认只转换新的JavaScript句法（syntax），而不转换新的API，比如Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法（比如Object.assign）都不会转码。

只要引入了babel-polyfill你可以大胆的用ES6。

安装方法：
```bash
$ npm install --save babel-polyfill
```
使用方法：
```javascript
// 脚本头部加上
import 'babel-polyfill';
// 或者
require('babel-polyfill');
```

一般babel-polyfill都是在babel-plugin-transform-runtime的基础上使用。

Babel默认不转码的API详单[definitions.js](https://github.com/babel/babel/blob/master/packages/babel-plugin-transform-runtime/src/definitions.js)