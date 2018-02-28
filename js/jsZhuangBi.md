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