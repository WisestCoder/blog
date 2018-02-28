### 继承的6种方式

#### 1. 原型链

拿父类实例来充当子类原型对象
```javascript
function Super(){
  // ...
}
function Sub(){
  // ...
}
Sub.prototype = new Super(); 
```

缺点：
1. 因为来自原型对象的引用属性是所有实例共享的，所以子类1对父类的属性进行改变，子类2也会跟着改变
2. 创建子类实例时，无法向父类构造函数传参

#### 2. 构造函数

借父类的构造函数来增强子类实例，等于是把父类的实例属性复制了一份给子类实例装上了（完全没有用到原型）
```javascript
function Super(val){
  // ...
}
function Sub(val){
  Super.call(this, val);
  // ...
}
```

缺点：子类之间无法实现函数复用，因为方法都在构造函数中定义。

#### 3. 组合继承

把实例函数都放在原型对象上，以实现函数复用。同时还要保留借用构造函数方式的优点，通过Super.call(this);继承父类的基本属性和引用属性并保留能传参的优点；通过Sub.prototype = new Super();继承父类函数，实现函数复用。
```javascript
function Super(){
  // ...
}
//  在此处声明函数
Super.prototype.fun1 = function(){};
Super.prototype.fun2 = function(){};
// ...
function Sub(val){
  Super.call(this, val);  // 第一次调用父类构造函数
  // ...
}
Sub.prototype = new Super();  // 第二次调用父类构造函数
```

缺点：子类原型上有一份多余的父类实例属性，因为父类构造函数被调用了两次，生成了两份，而子类实例上的那一份屏蔽了子类原型上的（会先在实例上找，找不到再去原型链上找）。

#### 4. 原型式继承

借助原型可以基于已有的对象创建新对象，同时还不必须因此创建自定义的类型。
```javascript
function object(o) {
  function F(){}
  F.prototype = o;
  return new F();
}

var person = {
  name: 'EvanChen'
};
var anotherPerson = object(person);

console.log(anotherPerson.name); // 'EvanChen'
```

缺点：必须有一个对象可以作为另一个对象的基础

#### 5. 寄生式继承

寄生式继承是与原型式继承紧密相关的一种思路，创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后再像真正是它做了所有工作一样返回对象。

```javascript
function createAnother(original) {
  var clone = object(original);
  clone.fun1 = function () {
    // ...
  };
return clone;
}
var person = {
  name: 'EvanChen'
};
var anotherPerson = createAnother(person);
anotherPerson.fun1();///"hi"
```

#### 6. 寄生组合继承

去除了原型对象上多余的那份父类实例属性，通过借用构造函数来继承属性，通过原型链的混成形式来继承。
```javascript
function inheritProperty(subType, superType) {
  var prototype = object(superType.prototype); //创建对象
  prototype.constructor = subType; //增强对象
  subType.prototype = prototype; //指定对象
}

function Super(){
  // ...
}
//  在此处声明函数
Super.prototype.fun1 = function(){};
Super.prototype.fun2 = function(){};
// ...
function Sub(val){
  Super.call(this, val);
  // ...
}
inheritProperty(Sub, Super)
```
开发人员普遍认为寄生组合式继承是最理想的继承范式。

而我不这么认为，我喜欢美滋滋的用es6的class。

