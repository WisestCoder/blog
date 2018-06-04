## 函数式编程

### 特性

#### 函数是“一等公民”

我们把函数类型分成三类：
- first-class 类型的值可以传给子程序作为参数，可以从子程序里返回，可以赋给变量。大多数程序设计语言里，整型、字符类型等简单类型都是一级的。
- second-class 该等级类型的值可以传给子程序作为参数，但是不能从子程序里返回，也不能赋给变量。
- third-class 该等级类型的值连作为参数传递也不行。

由此我们可以理解 first-class 函数的几个原则：
- 可以被命名为变量
  ```javascript
  const add = function (x, y) {
    return x + y;
  }
  ```
- 可以作为程序的参数
  ```javascript
  const base = [1, 2, 3];
  const double = function (num) {
    return num * 2;
  }
  const result = base.map(double);
  // double 就是 map 函数的参数
  ```
- 可以作为程序的返回值
  ```javascript
  function currying (fn, ...ahead) {
    return function (...behind) {
      return fn(...ahead, ...behind);
    }
  }
  const add = function(x, y) {
    return x + y;
  }
  const add2 = currying(add, 2);
  const num = 1;
  const result = add2(add2(add2(add2(num))));
  ```
- 可以包含在数据结构里
  ```javascript
  const add = function(x, y) {
    return x + y;
  }
  const obj = {
    add: add,
  }
  const ary = [add];
  ```

#### 没有副作用
在计算器科学中，函数副作用指当调用函数时，除了返回函数值之外，还对主调用函数产生附加的影响。例如修改全局变量（函数外的变量）或修改参数。

函数式编程强调没有"副作用"，意味着函数要保持独立，所有功能就是返回一个新的值，没有其他行为，尤其是不得修改外部变量的值。

#### 只用表达式
“表达式”（expression）是一个单纯的运算过程，总是有返回值；“语句”（statement）是执行某种操作，没有返回值。函数式编程要求，只使用表达式，不使用语句。也就是说，每一步都是单纯的运算，而且都有返回值

#### 引用透明
产生副作用的对立就是纯函数。当我们的程式都应用纯函数，那我们这段程式就是引用透明的。因为我们可以把程式中的任何一段函数代码，替换成它所得到的结果。如果用一句话来说：如果程式中的一段代码可以替换成它执行后的结果，而且程式的结果不会有影响，那我们就说对这段代码的引用是透明的。

### 抽象
[把问题抽象](https://github.com/sunyongjian/FP-Code/tree/master/src/introduction)

### 衍生

#### 高阶函数(HOF)

High Order Functions，即高阶函数，至少满足其下两个条件之一：
- 以一个函数作为参数
- 以一个函数作为返回结果

函数可以作为返回值
```javascript
const add = function(x) {
  return function(y) {
    return x + y;
  }
}
console.log(add(1)(2));
```

函数作为参数，比如操作数组的 map，reduce 函数，都是此类
```javascript
const x = [1, 2, 3];
const y = x.map(function(item) {
  return item + 1;
})
```

偏函数应用
```javascript
const isType = function (type) {
  return function(obj) {
    if(typeof type !== 'string') {
      return new Error('type need string');
    }
    const str = type[0].toUpperCase() + type.slice(1);
    return Object.prototype.toString.call(obj) === `[object ${str}]`;
  }
}

const isNumber = isType('number');
console.log(isNumber(1));
```
isType 返回一个新的函数。不过这种情况也叫偏函数，即把函数的某些参数值固定住，返回一个新的函数，调用这个新函数会更简单。

节流与防抖
```javascript
const throttle = wait => fn => {
  var timer;
  return (...args) => {
    if (!timer) {
      timer = setTimeout(() => timer = null, wait);
      return fn(...args);
    }
  }
}

const debounce = wait => fn => {
  var timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn(...args);
    }, wait)
  }
}
```

#### 柯里化(Curring)
[柯里化](https://github.com/wisestcoder/blog/blob/master/js/curry.md)

#### 函数组合(Compose)
[组合](https://github.com/wisestcoder/blog/blob/master/js/compose.md)

#### 不可变数据(Immutable data)

为什么要有不可变数据
```javascript
var x = {
    a: 1
}
var y = x;
x.a = 2;
console.log(y); //{ a: 2 }
```
这在我们刚开始学 js 的时候就知道了，x = y 是对象赋值引用，两者公用一个对象的空间，所以 x 改动了，y 自然也改变。

数组也是一样的：
```javascript
var ary = [1, 2, 3];
var list = ary;
ary.push(4);
console.log(list); // [1, 2, 3, 4]
```

在 js 中像上面 y = x 的操作显然是不符合函数式编程规范的，这就意味着我们要给新对象（数据）创建一个新的引用，也就是需要数据拷贝。

Object.assign
```javascript
const x = { a: 1 };

const y = Object.assign({}, x);
x.a = 11;
console.log(y); // { a: 1 }
```

Object.freeze
```javascript
const x = { a: 1, b: { c: 2 } };
const y = Object.freeze(x);
x.a = 11;
console.log(y);

x.b.c = 22;

console.log(y); // { a: 1, b: { c: 22}}
```
freeze，看起来是真的“冻结”了，不可变了，其实效果是一样的，为了效率，做的浅拷贝。

解构
```javascript
const x = { a: 1, b: { c: 2 } };
const y = { ...x };
x.a = 11;
console.log(y);

x.b.c = 22;

console.log(y)

const x = [1, 2, [3, 4]];
const y = [...x];
x[2][0] = 33;
console.log(y); // [1, 2, [33, 4]]
```

deepCopy
```javascript
function clone(obj) {
  // 类型判断。 isActiveClone 用来防止重复 clone，效率问题。
  if (obj === null || typeof obj !== 'object' || 'isActiveClone' in obj) {
    return obj;
  }

  //可能是 Date 对象
  const result = obj instanceof Date ? new Date(obj) : {};

  for (const key in obj) {
    if (Object.prototype.hasOwnProperty.call(obj, key)) {
      obj['isActiveClone'] = null;
      result[key] = clone(obj[key]);
      delete obj['isActiveClone'];
    }
  }

  return result;
}

var x = {
  a: 1,
  b: 2,
  c: {
    d: 3
  }
}
console.log(clone(x));
```

JSON序列化
```javascript
const x = {
  a: function() {
    console.log('aaa')
  },
  b: NaN,
}

const y = JSON.parse(JSON.stringify(x));
console.log(y.b);
y.a();
// 不支持 NaN、正则、function等
```

Library 通常实现 deepCopy 的库：lodash、underscore、$.extend(true, )... 目前最好用的是 immutable.js。

[immutable.js配合react](https://github.com/wisestcoder/blog/blob/master/react/immutable.md)



### 应用

#### 缓存函数(Memoization)
[memoization缓存技术](https://github.com/wisestcoder/blog/blob/master/js/memoization.md)