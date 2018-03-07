### Object.assign详解

#### 定义

Object.assing()方法用来拷贝对象的可枚举的属性，返回值是修改后的目标对象，原目标对象也会被修改

```javascript
Object.assign(target, ...sources)
```

- target: 目标对象
- soucres：要拷贝进目标对象的属性，可以是多个属性

```javascript
let myObj = {
  a: 111
};
let newObj = Object.assign(myObj, {b: 222});
console.log(myObj); //  {a: 111, b: 222}
console.log(newObj);//  {a: 111, b: 222}
```

#### 详解
如果sources中属性键名与目标对象的键名相同，则会覆盖目标对象的键值

```javascript
let myObj = {
  a: 111
};
console.log(myObj); //{a : 111}
Object.assign(myObj, {a: 222});
console.log(myObj) // {a: 222}
```

后传入的键值会覆盖系先传入的相同键名的键值

```javascript
let myObj = {
  a: 111
}
console.log(myObj); //{a : 111}
Object.assign(myObj, {a: 222},{a :333});
console.log(myObj) // {a: 333}
```

这个方法只能复制可枚举的属性和自身的属性，对目标对象target使用了get和set方法

#### 深拷贝

Object.assing()方法可以实现一级层面的深拷贝：

```javascript
let myObj = {
  a: 111
};
let newObj = Object.assign({}, myObj);
myObj.a = 222;
console.log(myObj.a); // 222
console.log(newObj.a); // 111
```

但是对于多层次的拷贝就是浅拷贝了：

```javascript
let myObj = {
  a: 111,
  b: {
    aa: 111
  }
};
let newObj = Object.assign({}, myObj);
myObj.b.aa = 222;
console.log(myObj.b.aa); // 222
console.log(newObj.b.aa); // 222
```