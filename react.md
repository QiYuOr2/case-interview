---
description: React相关面试题
---

# React

## 生命周期有哪些

React 生命周期分为三个阶段：

- 挂载阶段（Mount），组件第一次在 DOM 树中被渲染的过程
- 更新阶段（Update），组件状态变化，重新渲染的过程
- 卸载阶段（Unmount），组件从 DOM 树中被移除的过程

### 挂载阶段

- `constructor`
  加载时调用一次，可以用来初始化 state 和绑定事件处理函数
- `static getDerivedStateFromProps`
  有两个参数 state 和 props，分别为接受的新参数和当前组件的 state，这个函数会返回一个新的对象来更新当前 state，不需要更新可以返回 null
  在挂载、接收到新的 props 或调用了 setState 和 forceUpdate 被调用
  如当接收到新的属性想修改 state ，就可以使
- `render`
  根据 state 和 props 渲染组件
- `componentDidMount`
  组件挂载之后调用一次，一般在这里请求数据

### 更新阶段

- `static getDerivedStateFromProps`
- `shouldComponentUpdate`
  当 props 或 state 发生变化时，在渲染前调用，return true 就会更新 dom，return false 能阻止更新
- `render()`
- `getSnapshotBeforeUpdate`
  在最近一次的渲染提交到 DOM 节点之前调用，返回一个值，作为 componentDidUpdate 的第三个参数。它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）
- `componentDidUpdate`
  会在更新后会被立即调用，首次渲染不会执行此方法。 当组件更新后，可以在此处对 DOM 进行操作、 进行网络请求 。

### 卸载阶段

- `componentWillUnmount`
  在组件卸载及销毁之前直接调用， 在此方法中执行必要的清理操作，例如，清除 timer，取消网络请求等等。

### 错误处理

- `static getDerivedStateFromErr`
  在渲染阶段调用，它将抛出的错误作为参数，并返回一个值来更新 state
- `componentDidCatch`
  在提交阶段被调用，用于记录错误

## 废除了哪些生命周期函数

- `componentWillMount`
- `componentWillReceiveProps`
- `componentWillUpdate`

## 高阶组件、Render Props、Hooks

这三种都是为了复用状态逻辑而出现的解决方案

### 高阶组件

高阶组件（HOC）是 React 中用于复用组件逻辑的一种高级技巧。HOC 自身不是 React API 的一部分，它是一种基于 React 的组合特性而形成的设计模式。

HOC 接收一个组件和额外的参数（如果需要），返回一个新的组件

```javascript
// hoc的定义
function withSubscription(WrappedComponent, selectData) {
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        data: selectData(DataSource, props)
      };
    }
    // 一些通用的逻辑处理
    render() {
      // ... 并使用新数据渲染被包装的组件!
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };

// 使用
const BlogPostWithSubscription = withSubscription(BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id));
```

优点：不影响内层组件的状态，降低耦合度
缺点：HOC 传递给被包裹组件的 props 容易和被包裹后的组件重名，进而被覆盖

### Render Props

将一个组件内的 state 作为 props 传递给调用者, 调用者可以动态的决定如何渲染

```javascript
// 创建
class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.state = { x: 0, y: 0 };
  }

  // 组件逻辑

  render() {
    return <div>{this.props.render(this.state)}</div>;
  }
}

// 调用
<Mouse
  render={(mouse) => (
    <p>
      鼠标的位置是{mouse.x}, {mouse.y}
    </p>
  )}
/>;
```

优点：将组件内的 state 作为 props 传递给调用者，将渲染逻辑交给调用者
缺点：无法在 render 语句外访问数据

### Hooks

在函数组件中使用 state 和其他 React 特性。通过自定义 Hook，可以从组件中提取出可复用的代码逻辑。

```javascript
function useMouse() {
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);

  useEffect(() => {
    // 业务逻辑
  }, [x, y]);

  return { x, y };
}

const { x, y } = useMouse();

<p>
  鼠标的位置是{mouse.x}, {mouse.y}
</p>;
```

注意：Hooks 只能在组件顶层使用，不能在循环、条件、嵌套函数中使用
