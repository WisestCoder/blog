### 4种保持state不可变的方法

错误例子：
```javascript
updateState(event) {
 const {name, value} = event.target;
 let user = this.state.user; // this is a reference, not a copy...
 user[name] = value; // so this mutates state 🙀
 return this.setState({user});
}
```
> The concern is on line 4. Line 4 actually mutates state because the user variable is a reference to state. React state should be treated as immutable.

react文档描述：
> Never mutate `this.state` directly, as calling `setState()` afterwards may replace the mutation you made. Treat `this.state` as if it were immutable.

意思就是说不要直接操作`this.state`，而是用`setState()`用代替。

为什么呢？官方给出的解释如下：
> 1. SetState batches work behind the scenes. This means a manual state mutation may be overridden when setState is processed.

> 2. If you declare a shouldComponentUpdate method, you can’t use a === equality check inside because the object reference will not change. So the approach above has a potential performance impact as well.

大概意思就是说：
  1. `setState`是异步的，所以我们手动改变`this.state`可能会覆盖掉`setState`。
  2. 由于我们直接nextState改变了`this.state`,导致在`shouldComponentUpdate`的生命周期中，`this.state`和`nextState`的值永远是equal的，所以在这个生命周期里，做不了一些判断操作。

我们可以拿第二点举个例子：
```javascript
class Test extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      user: {
        count: 0
      }
    };
  }
  shouldComponentUpdate(nextProps, nextState) {
    console.log('当前state:', this.state);
    console.log('改变的state:', nextState);
    return this.state.user.count !== nextState.user.count;
  }
  render() {
    return (
      <div>
        <span>{ this.state.user.count }</span><br />
        <button onClick={() => {
          let user = this.state.user;
          user.count = user.count + 1;
          this.setState({ user });
        }}>点击加1</button><br />
      </div>
    );
  }
}
```
结果如图：
![](https://github.com/wisestcoder/blog/blob/master/react/images/fourImmutable.png)

可见操作直接操作`this.state`的后果是非常严重的。

**方法1：Object.assign**
```javascript
updateState(event) {
 const {name, value} = event.target;
 let user = Object.assign({}, this.state.user);
 user[name] = value;
 return this.setState({user});
}
```

**方法2：Object Spread**
```javascript
updateState(event) {
 const {name, value} = event.target;
 let user = {...this.state.user, [name]: value};
 this.setState({user});
}
```

**方法3：Immutability Helper**
```javascript

// Import at the top:
import update from 'immutability-helper';

updateState({target}) {
 let user = update(this.state.user, {$merge: {[target.name]: target.value}});
 this.setState({user});
```

**方法4：Immutable.js**
```javascript
// At top, import immutable
import { Map } from 'immutable';

// Later, in constructor...
this.state = {
  // Create an immutable map in state using immutable.js
  user: Map({ firstName: 'Cory', lastName: 'House'})
};

updateState({target}) {
 // this line returns a new user object assuming an immutable map is stored in state.
 let user = this.state.user.set(target.name, target.value);
 this.setState({user});
}
```