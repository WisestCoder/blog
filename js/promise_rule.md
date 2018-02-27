### Promise/A+规范

Promise表示一个异步操作的最终结果。与Promise最主要的交互方法是通过将函数传入它的then方法从而获取得Promise最终的值或Promise最终最拒绝（reject）的原因。

#### 1. 术语

- `promise` 是一个包含了兼容promise规范then方法的对象或函数，
- `thenable` 是一个包含了then方法的对象或函数。
- `value` 是任何Javascript值。 (包括 undefined, thenable, promise等).
- `exception` 是由throw表达式抛出来的值。
- `reason` 是一个用于描述Promise被拒绝原因的值。

#### 2. 要求

**2.1 Promise状态**

一个Promise必须处在其中之一的状态：pending, fulfilled 或 rejected.

1. 如果是pending状态,则promise：
  - 可以转换到fulfilled或rejected状态。
2. 如果是fulfilled状态,则promise：
  - 不能转换成任何其它状态。
  - 必须有一个值，且这个值不能被改变。
3. 如果是rejected状态,则promise可以：
  - 不能转换成任何其它状态。
  - 必须有一个原因，且这个值不能被改变。
  - ”值不能被改变”指的是其identity不能被改变，而不是指其成员内容不能被改变。

**2.2 then 方法**

一个Promise必须提供一个then方法来获取其值或原因。

Promise的then方法接受两个参数：

```javascript
promise.then(onFulfilled, onRejected)
```

1. onFulfilled 和 onRejected 都是可选参数：
  - 如果onFulfilled不是一个函数，则忽略之。
  - 如果onRejected不是一个函数，则忽略之。
2. 如果onFulfilled是一个函数:
  - 它必须在promise fulfilled后调用， 且promise的value为其第一个参数。
  - 它不能在promise fulfilled前调用。
  - 不能被多次调用。
3. 如果onRejected是一个函数,
  - 它必须在promise rejected后调用， 且promise的reason为其第一个参数。
  - 它不能在promise rejected前调用。
  - 不能被多次调用。
4. onFulfilled 和 onRejected 只允许在 execution context 栈仅包含平台代码时运行。
5. onFulfilled 和 onRejected 必须被当做函数调用 (i.e. 即函数体内的 this 为undefined)。
6. 对于一个promise，它的then方法可以调用多次.
  - 当promise fulfilled后，所有onFulfilled都必须按照其注册顺序执行。
  - 当promise rejected后，所有OnRejected都必须按照其注册顺序执行。
7. then 必须返回一个promise。

```javascript
promise2 = promise1.then(onFulfilled, onRejected);
``` 

  - 如果onFulfilled 或 onRejected 返回了值x, 则执行Promise 解析流程[[Resolve]](promise2, x).
  - 如果onFulfilled 或 onRejected抛出了异常e, 则promise2应当以e为reason被拒绝。
  - 如果 onFulfilled 不是一个函数且promise1已经fulfilled，则promise2必须以promise1的值fulfilled.
  - 如果 OnReject 不是一个函数且promise1已经rejected, 则promise2必须以相同的reason被拒绝.
  