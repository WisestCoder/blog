### console调试技巧
```javascript
console.log  // 正常打印输出

console.assert(expression, message)  // 如果 expression 表达式的结果为 false，console.assert 将会抛出错误;且不会停止执行之后的代码

console.count  // 会计算相同表达式执行过多少次的console.log

console.table  // 会把传入的参数按照表格的形式输出

console.group / console.groupEnd  // 会对代码进行分组

console.trace  // 打印调用栈

console.warn  // 打印警告

console.error  // 打印错误

console.time / console.timeEnd  // 打印一个操作所花费的准确时间
```