### immutable.js

![](https://github.com/dushao103500/blog/blob/master/react/images/immutable.gif)

Immutable Data 就是一旦创建，就不能再被更改的数据。对immutable对象的任何修改或添加删除操作都会返回一个新的immutable对象。

Immutable 实现的原理是 Persistent Data Structure（持久化数据结构），也就是使用旧数据创建新数据时，要保证旧数据同时可用且不变。同时为了避免 deepCopy 把所有节点都复制一遍带来的性能损耗，Immutable 使用了 Structural Sharing（结构共享），即如果对象树中一个节点发生变化，只修改这个节点和受它影响的父节点，其它节点则进行共享。

[immutable详解及React中实践 ](https://github.com/camsong/blog/issues/3)

**主要解决**
- 解决对象操作时数据变化的不可控。
- 解决对象进行深层复制、比较等操作时的性能问题。

**优点**
- Immutable 降低了 Mutable 带来的复杂度。
- 数据可记忆性。
- 增量式的获取数据，不会丢失数据。

**缺点**
- 用空间换取时间，而且需要引入资源文件。
- immutable对象不同于原生对象，所以容易与原生对象混淆。 （定义immutable对象时尽量使用不同的命名规则）

**与React搭配使用，Pure Render**
React在进行render之前，会调用shouldComponentUpdate生命周期，所以可以在这个生命周期中避免不必要的渲染。
```javascript
import { is } from 'immutable';

shouldComponentUpdate: (nextProps = {}, nextState = {}) => {
  const thisProps = this.props || {}, thisState = this.state || {};

  if (Object.keys(thisProps).length !== Object.keys(nextProps).length ||
      Object.keys(thisState).length !== Object.keys(nextState).length) {
    return true;
  }

  for (const key in nextProps) {
    if (!is(thisProps[key], nextProps[key])) {
      return true;
    }
  }

  for (const key in nextState) {
    if (thisState[key] !== nextState[key] || !is(thisState[key], nextState[key])) {
      return true;
    }
  }
  return false;
}
```

**setState时的示例代码**
```javascript
const Component = React.createClass({
  getInitialState() {
    return {
      data: { times: 0 }
    }
  },
  handleAdd() {
    this.setState(({ data }) => ({
      data: data.update('times', v => v + 1)
    }));
  }
});
```


