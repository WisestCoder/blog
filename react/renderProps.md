### 使用Render props代替HOC

**发展：** Mixins -> HOC -> Render Props

假设现在要实现一个简单的 React 应用，需要跟踪并在页面上实时显示鼠标位置，并让这段代码可复用。

Mixins示例：
```javascript
import React from 'react'
import ReactDOM from 'react-dom'

// mixin 中含有了你需要在任何应用中追踪鼠标位置的样板代码。
// 我们可以将样板代码放入到一个 mixin 中，这样其他组件就能共享这些代码
const MouseMixin = {
  getInitialState() {
    return { x: 0, y: 0 }
  },

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    })
  }
}

const App = React.createClass({
  // 使用 mixin！
  mixins: [ MouseMixin ],
  
  render() {
    const { x, y } = this.state

    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        <h1>The mouse position is ({x}, {y})</h1>
      </div>
    )
  }
})

ReactDOM.render(<App/>, document.getElementById('app'))
```

HOC示例：
```javascript
import React from 'react'
import ReactDOM from 'react-dom'

const withMouse = (Component) => {
  return class extends React.Component {
    state = { x: 0, y: 0 }

    handleMouseMove = (event) => {
      this.setState({
        x: event.clientX,
        y: event.clientY
      })
    }

    render() {
      return (
        <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
          <Component {...this.props} mouse={this.state}/>
        </div>
      )
    }
  }
}

const App = React.createClass({
  render() {
    // 现在，我们得到了一个鼠标位置的 prop，而不再需要维护自己的 state
    const { x, y } = this.props.mouse

    return (
      <div style={{ height: '100%' }}>
        <h1>The mouse position is ({x}, {y})</h1>
      </div>
    )
  }
})

// 用 withMouse 包裹组件，它就能获得 mouse prop
const AppWithMouse = withMouse(App)

ReactDOM.render(<AppWithMouse/>, document.getElementById('app'))
```

Render Props示例：
```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import PropTypes from 'prop-types'

// 与 HOC 不同，我们可以使用具有 render prop 的普通组件来共享代码
class Mouse extends React.Component {
  static propTypes = {
    render: PropTypes.func.isRequired
  }

  state = { x: 0, y: 0 }

  handleMouseMove = (event) => {
    this.setState({
      x: event.clientX,
      y: event.clientY
    })
  }

  render() {
    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        {this.props.render(this.state)}
      </div>
    )
  }
}

const App = React.createClass({
  render() {
    return (
      <div style={{ height: '100%' }}>
        <Mouse render={({ x, y }) => (
          // render prop 给了我们所需要的 state 来渲染我们想要的
          <h1>The mouse position is ({x}, {y})</h1>
        )}/>
      </div>
    )
  }
})

ReactDOM.render(<App/>, document.getElementById('app'))
```

任何 HOC 都能使用 render prop 替代。