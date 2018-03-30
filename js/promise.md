
### promise实现

我们通过逆推法，一步步去实现promise。

首先观察promise的用法，如下：
```javascript
const promsie = new Promsie((resolve, reject) => {
  if ...
    resolve(xx);
  else
    reject(xx);
})

promise.then((value) => {
  ...
});
```

通过观察上面的用法，我们大概可以做一下猜想：
1. `Promsie`是一个function，且接收一个回调函数为参数
2. `Promsie`对象有一个`then`方法，`then`方法接收一个回调函数为参数
3. `resolve`和`reject`方法，执行的时候，会将参数带给`then`方法中的回调函数，并触发执行

因此我们可以得到以下代码：
```javascript
function Promise(fn) {
  var value = null,
    callbacks = [];  // callbacks为数组，因为可能同时有很多个回调

  this.then = function (onFulfilled) {
    callbacks.push(onFulfilled);
    return this;  // 以便可以进行链式操作
  };

  function resolve(value) {
    callbacks.forEach(function (callback) {
      callback(value);
    });
  }

  fn(resolve);
}
```

上述代码可能还存在一个问题：如果在then方法注册回调之前，resolve函数就执行了，怎么办？

比如promise内部的函数是同步函数：
```javascript
const dosomething = () => {
  return new Promise(function (resolve) {
    resolve(123);
  });
}
dosomething().then(function (id) {
  ...
});
```
我们可以给resolve函数添加一个延时执行，首先我们想到的是`setTimeout(fn, 0);`
```javascript
function resolve(value) {
  setTimeout(function() {
    callbacks.forEach(function (callback) {
      callback(value);
    });
  }, 0);
} 
```

如果Promise异步操作已经成功，这时，在异步操作成功之前注册的回调都会执行，但是在Promise异步操作成功这之后调用的then注册的回调就再也不会执行了，此时我们必须引入状态机制。

首先明白状态机制是啥？
> Promises/A+ 2.1规范
>> pending可以转化为fulfilled或rejected并且只能转化一次，也就是说如果pending转化到fulfilled状态，那么就不能再转化到rejected。并且fulfilled和rejected状态只能由pending转化而来，两者之间不能互相转换。

经过改进之后，我们的代码如下：
```javascript
function Promise(fn) {
  var state = 'pending',
      value = null,
      callbacks = [];

  this.then = function (onFulfilled) {
    return new Promise(function (resolve) {
      function handle(value) {
        var ret = isFunction(onFulfilled) && onFulfilled(value) || value;
        resolve(ret);
      }
      if (state === 'pending') {
        callbacks.push(handle);
      } else if(state === 'fulfilled'){
        handle(value);
      }
    });
  };

  function resolve(newValue) {
    value = newValue;
    state = 'fulfilled';
    setTimeout(function () {
      callbacks.forEach(function (callback) {
        callback(value);
      });
    }, 0);
  }

  fn(resolve);
}
```

那么这里问题又来了，如果用户再then函数里面注册的仍然是一个Promise，如下：
```javascript
const dosomethingmore = (value) => {
  return new Promise((resolve) => {
    ...
    resolve(newValue);
  });
}

dosomething()
  .then(dosomethingmore)
  .then((value) => {
    ...
  });
```
这就是所谓的链式Promise。

> 链式Promise是指在当前promise达到fulfilled状态后，即开始进行下一个promise

经过改进，我们得到如下代码：
```javascript
function Promise(fn) {
  var state = 'pending',
      value = null,
      callbacks = [];

  var isFunction = function(fn) {
    return Object.prototype.toString.call(fn) === '[object Function]';
  }

  this.then = function (onFulfilled) {
    return new Promise(function (resolve) {
      function handle(value) {
        var ret = isFunction(onFulfilled) && onFulfilled(value) || value;
        // 当ret为promise对象时，手动执行.then方法
        if(ret && typeof isFunction(ret.then)) {
          ret.then(function(value){
            resolve(value);
          });
        } else {
          resolve(ret);
        }
      }
      if (state === 'pending') {
        callbacks.push(handle);
      } else if(state === 'fulfilled') {
        handle(value);
      }
    });
  };

  function resolve(newValue) {
    value = newValue;
    state = 'fulfilled';
    setTimeout(function () {
      callbacks.forEach(function (callback) {
        callback(value);
      });
    }, 0);
  }

  fn(resolve);
}
```

我们继续加入失败的状态，得到如下代码：
```javascript
function Promise(fn) {
  var state = 'pending',
      value = null,
      reason = null,
      callbacks = [],
      errCallbacks = [];

  function isFunction (fn) {
    return Object.prototype.toString.call(fn) === '[object Function]';
  }

  this.then = function (onFulfilled, onRejected) {
    return new Promise(function (resolve, reject) {
      function handle(value) {
        var ret = isFunction(onFulfilled) && onFulfilled(value) || value;
        // 当ret为promise对象时，手动执行.then方法
        if(ret && isFunction(ret.then)) {
          ret.then(function(value){
            resolve(value);
          });
        } else {
          resolve(ret);
        }
      }
      function errback(reason) {
        reason = isFunction(onRejected) && onRejected(reason) || reason;
        reject(reason);
      }
      if (state === 'pending') {
        callbacks.push(handle);
        errCallbacks.push(errback);
      } else if (state === 'fulfilled') {
        handle(value);
      } else if (state === 'rejected') {
        errback(reason);
      }
    });
  };

  function resolve(newValue) {
    value = newValue;
    state = 'fulfilled';
    setTimeout(function () {
      callbacks.forEach(function (callback) {
        callback(value);
      });
    }, 0);
  }

  function reject(newReason) {
    reason = newReason;
    state = 'rejected';
    setTimeout(function () {
      callbacks.forEach(function (callback) {
        callback(reason);
      });
    }, 0);
  }

  fn(resolve);
}
```
