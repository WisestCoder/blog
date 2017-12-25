### PureRenderMixin

React的渲染函数式纯函数（当传给它同样的props和state，它渲染出同样的效果），所以部分场景下可以利用一些插件来提升性能。

如果更新的props和旧的一样，这个时候很明显UI不会变化，但是React还是要进行虚拟DOM的diff，这个diff就是多余的性能损耗，而且在DOM结构比较复杂的情况，整个diff会花费较长的时间。

侧重的优化点是UI需不需要更新。

**ES6中的写法**
```javascript
import PureRenderMixin from 'react-addons-pure-render-mixin';
class FooComponent extends React.Component {
      constructor(props) {
        super(props);
        this.shouldComponentUpdate = PureRenderMixin.shouldComponentUpdate.bind(this);
      }

      render() {
        return <div className={this.props.className}>foo</div>;
      }
}
```

PureRenderMixin的原理就是它扩展了shouldComponentUpdate，在shouldComponentUpdate内它比较当前的props、state和接下来的props、state，当两者相等的时候返回false，这样组件就不会进行虚拟DOM的diff。

**这里需要注意**

PureRenderMixin内进行的仅仅是浅比较对象。如果对象包含了复杂的数据结构，深层次的差异可能会产生误判。仅用于拥有简单props和state的组件。

**源码**
```javascript
'use strict';
var shallowCompare = require('./shallowCompare');
var ReactComponentWithPureRenderMixin = {
  shouldComponentUpdate: function (nextProps, nextState) {
    return shallowCompare(this, nextProps, nextState);
  }
};
module.exports = ReactComponentWithPureRenderMixin;
```

**shallowCompare方法**
```javascript
'use strict';
var shallowEqual = require('fbjs/lib/shallowEqual');
function shallowCompare(instance, nextProps, nextState) {
  return !shallowEqual(instance.props, nextProps) || !shallowEqual(instance.state, nextState);
}
 
module.exports = shallowCompare;
```

**shallowEqual方法（比较两个对象是否相等，递归度为1）**
```javascript
function shallowEqual(objA, objB) {
  if (objA === objB) {  //检查对象引用的相等性
    return true;
  }
  if (typeof objA !== 'object' || objA === null || typeof objB !== 'object' || objB === null) {
    return false;
  }
  var keysA = Object.keys(objA);//返回对象可被枚举的属性
  var keysB = Object.keys(objB);
  if (keysA.length !== keysB.length) {
    return false;
  }
  // Test for A's keys different from B.
  var bHasOwnProperty = hasOwnProperty.bind(objB);// bHasOwnProperty(xx) === objB.hasOwnProperty(xx)
  for (var i = 0; i < keysA.length; i++) {
    if (!bHasOwnProperty(keysA[i]) || objA[keysA[i]] !== objB[keysA[i]]) {
      return false;
    }
  }
  return true;
}
 
module.exports = shallowEqual;
```