### memoization缓存技术

Memoization是一种将函数返回值缓存起来的方法，Memoization原理非常简单，就是把函数的每次执行结果都放入一个键值对(数组也可以，视情况而定)中，在接下来的执行中，在键值对中查找是否已经有相应执行过的值，如果有，直接返回该值，没有才 真正执行函数体的求值部分。很明显，找值，尤其是在键值对中找值，比执行函数快多了。

memoization适用于递归计算场景，例如fibonacci（斐波那契数列）数值的计算。

```javascript
// 未使用memoization技术
function startFibonacci(count) {
  var cal = 0;
  var startTime = performance.now();
  function fibonacci(n) {
    cal ++;
    //console.log(count);
    if (n == 0 || n == 1) {
      return n;
    } else {
      return fibonacci(n - 1) + fibonacci(n - 2);
    }
  }
  console.log("结果：", fibonacci(count));
  console.log("执行次数：", cal);
  console.log("执行时间：", performance.now() - startTime);
}
```

经过测试，发现随着传入的N值越来越大，计算量也呈现为指数增长，如图：

![](https://github.com/dushao103500/blog/blob/master/js/images/memoization.png)

接下来我们使用memoization技术进行改造
```javascript
function startFibonacci(count) {
  let cal = 0;
  var startTime = performance.now();
  const memo = {};
  function fibonacci(n) {
    cal ++;
    let value;
    if (n in memo) {
      value = memo[n];
    } else {
      if (n == 0 || n == 1) {
        value = n;
      } else {
        value = fibonacci(n - 1) + fibonacci(n - 2);
      }
      memo[n] = value;
    }
    return memo[n];
  }

  console.log("结果：", fibonacci(count));
  console.log("执行次数：", cal);
  console.log("执行时间：", ((performance.now() - startTime) / 1000));
}
```

测试结果如图：
![](https://github.com/dushao103500/blog/blob/master/js/images/memoization2.png)

效果显著！

**memoization函数封装**
```javascript
function memoize(func) {
  let memo = {};
  let slice = Array.prototype.slice;
  return function() {
    let args = slice.call(arguments);
    if (args in memo) {
      return memo[args];
    } else {
      return memo[args] = func.apply(this, args);
    }
  };
}
```

注意：memoization只适用于纯函数。

