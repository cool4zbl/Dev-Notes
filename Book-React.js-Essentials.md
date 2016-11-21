## React.JS Essential Notes

---

### 虚拟 DOM
- React 没有采用双向数据绑定，而是使用虚拟 DOM；
- 虚拟 DOM 是真实 DOM 的一个快速的、仅存在于内存中的映射，它是一个抽象，JS + DOM as Reactive.
- 虚拟 DOM Diff Algo.

### Stateless / State Component
- 把组件分为两个关注点：如何处理用户界面的交互逻辑和如何呈现数据；
- 少数组件是有状态的，应该位于组件层级中的顶部。封装了所有交互逻辑，管理 UI，然后通过 `props` 将状态向下传递到无状态组件；
- 大多数组件为无状态。通过 `this.props` 从父组件接收状态并渲染数据;
- 每次更新 `state`，就会运行一遍 `render()`；

### React 事件
- React 使用驼峰命名规范定义事件处理函数；
- 默认情况下，React 会在冒泡阶段触发事件处理函数，但也可以在事件处理函数后 + `Capture` -> 捕获阶段触发，如 `onClickCapture`；
- React 在 `SyntheticEvent` 对象中包装了一个浏览器原生的事件，来保证所有被支持事件在 IE8+ 行为相同；
- `SyntheticEvent` 提供了与浏览器原生事件一致的 API。 `stopProgatation()` `preventDefault()` etc..
- 需要访问原生事件 -> `nativeEvent`;
- 各种事件属性 `onClick`... 传递到 `createElement()` 中并不会在渲染的 HTML 标记中创建内联的事件处理函数；
- React 并不会将事件处理函数绑定到 DOM 节点本身，相反，**它仅在最顶层使用一个事件监听器来监听所有事件，并把它们代理到相对应的事件处理函数；**


### 构建
- 从最顶级 React 元素开始，然后实现它的子组件，自顶向下来构建层级

### 组件生命周期
- 挂载 Mounting
- 更新 Updating
- 卸载 Unmounting


*Tips*:
- 全局对象不是 React 的一部分，也不是 APP 的一部分，不要在真是项目中将属性添加到一个全局对象中，因为可能会重写现有的属性，或者属性也可能被后面的代码重写。
- `componentDidMount()` 中整合 React / 3rd Library
- `componentWillReceiveProps()` 中调用 `this.setState()` 多少次都不会触发组件的额外渲染， React 内部做了优化，会把状态更新操作放在一起批量执行
- CSP 内容安全策略会阻止内联样式生效


### Flux
- 一种解决问题的思路，用于构建良好拓展性的 APP
- 数据单向流动
- 把 APP 的关注点分为四个逻辑块：
  - Action 动作
  - Dispatcher 分发器 核心，所有的动作都流经它，所有存储都向它注册
  - Store 存储
  - View 视图

