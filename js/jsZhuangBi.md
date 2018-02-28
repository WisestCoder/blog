### js装逼技巧

#### 利用按位与 & 判断奇偶数
```javascript
function fn(n) {
  if(n & 1) {
    //奇数
  } else {
    //偶数
  }
}
```

#### 利用按位或 | 取整
```javascript
1.1 | 0 // 1
-2.0 | 0 // -2

// 非数字，则根据值强转为 0 或者 1 再取整，不过这种没啥意义
null | 0 // 0
true | 0 // 1
```

#### 左移<< 求2的n次方
```javascript
1 << 2 // 4 即2的平方
1 << 3 // 8 即2的立方
1 << 4 // 16 即2的4次方
```

#### 无符号右移>>> 判断数的正负
```javascript
function isPos(n) {
  return n === n >>> 0
}
isPos(-1) // false
isPos(1) // true
```

#### 利用~~ 取整
```javascript
~~12.55 // 12
```

#### 利用+ 将字符串转为数字
```javascript
+'123' // 123
+'12.22' // 12
-'12.1' // -12.1
```

#### 利用!! 实现变量真假值的检测
```javascript
!!123 // true
!!'hello' // true
!!'false' // true
!!null // false
!!undefined // false
!!0 // false 注意，0也会转为false，在数字中，只有0会转为false，其它非0值，都会转为true
!!'' // false
!!NaN // false
```

#### 5种方式实现值交换
```javascript
1. var temp = a; a = b; b = temp; (传统，但需要借助临时变量)
2. a ^= b; b ^= a; a ^= b; (需要两个整数)
3. b = [a, a = b][0] (借助数组)
4. [a, b] = [b, a]; (ES6，解构赋值)
5. a = a + b; b = a - b; a = a - b; (小学奥赛题)\
```

#### 判断一个数是否未整数
```javascript
function isInt(x) {
  return (x ^ 0) === x
}
```
#### 判断两个数的符号是否相等
```javascript
function sameSign(a, b) {
  return (a ^ b) >= 0
}
```

#### 克隆数组
```javascript
arr.slice(0)
```

#### 求数组最大值最小值
```javascript
function maxArr(arr) {
  return Math.max.apply(null, arr)
}

function minArr(arr) {
  return Math.min.apply(null, arr)
}
```

#### 生成随机颜色
```javascript
function getRandomColor() {
    return `#${Math.random().toString(16).substr(2, 6)}`
}
```

#### 随机生成指定长度的字符串
```javascript
function randomStr(n) {
  let standard = 'abcdefghijklmnopqrstuvwxyz9876543210'
  let len = standard.length
  let result = ''

  for (let i = 0; i < n; i++) {
    result += standard.charAt(Math.floor(Math.random() * len))
  }

  return result
}
```

#### 我墙都不服就服你
```javascript
console.info("%c哈哈", "color: #3190e8; font-size: 30px; font-family: sans-serif");
```