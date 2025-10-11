https://juejin.cn/post/7503811658198286388
https://www.yuque.com/yuqueyonghua2m9wj/web_food/tpo1np

# react
## 核心
### 核心思路
React 核心思想：**声明式 + 组件化 + 单向数据流 + 虚拟 DOM**。

* **声明式**：只描述界面状态，React 自动更新 UI。
* **组件化**：界面拆分成可复用组件，各自管理状态和逻辑。
* **单向数据流**：数据从父到子，便于调试和维护。
* **虚拟 DOM**：先在内存中 diff，再最小化更新真实 DOM，提高性能。

### fiber
React Fiber 是一种新的协调引擎，用来提高 React 应用的性能和响应能力。
Fiber 通过增量渲染、可中断与恢复、链表结构和优先级调度等机制，使得 React 可以更灵活地处理大量更新和复杂组件树。

工作原理：
调度：Fiber 引入了新的调度机制，允许 React 根据任务的优先级来调度任务。React 会根据任务的紧急程度将任务放入不同的队列中，并按照队列的顺序执行任务。
渲染：在渲染阶段，React 会遍历组件树，并构建一个 Fiber 树。Fiber 树中的每个节点代表一个组件，并包含组件的状态、属性等信息。
更新：当组件的状态或属性发生变化时，React 会触发更新。Fiber 会根据变化的类型和优先级来决定如何更新组件。
提交：在更新阶段完成后，React 会将 Fiber 树转换为实际的 DOM 树，并提交给浏览器进行渲染。

#### 👉「Fiber 是如何终止（中断）渲染任务的？」

React 通过“时间切片（Time Slicing）”机制，在每个 Fiber 节点的执行过程中主动检查是否超时或有更高优先级任务，一旦发现，就主动中止当前任务，保存执行状态，稍后再恢复。

在 Fiber 架构中，渲染（Reconciliation）不再是递归调用，而是一个循环任务：
任务终止（中断）的触发点：shouldYield()

当 shouldYield() 返回 true：
表示主线程即将被浏览器用来渲染页面或响应用户操作

React 立即停止当前循环

保留当前 Fiber 的执行状态（nextUnitOfWork）

退出到浏览器主线程

等浏览器空闲后（下一帧），再继续执行上次中断的任务。

#### 为什么 React 可以“中断”而不会出错？

因为：

Fiber 是链表结构（非递归调用栈）

每个 Fiber 节点保存了完整的执行上下文（props、state、return 指针等）

所以中断后可以从当前节点恢复，不依赖于 JS 调用栈

这正是 Fiber 架构最大的设计亮点之一。



### 虚拟dom
React提出的一种解决方案，它是一个轻量级的JavaScript对象，用来描述真实DOM的结构和属性。
React通过比较虚拟DOM的差异，计算出需要更新的部分，然后再将这些部分更新到真实DOM上。
React虚拟DOM的原理是：
1. 首先，React将组件的状态和属性传入组件的render方法，得到一个虚拟DOM树。
2. 当组件的状态或属性发生变化时，React会再次调用render方法得到新的虚拟DOM树。
3. React会将新旧两棵虚拟DOM树进行比较，得到它们的不同之处。
4. React会将这些不同之处记录下来，然后批量的更新到真实的DOM树上。

React通过虚拟DOM树的比较，避免了直接操作真实DOM树带来的性能问题，因为直接操作真实DOM树会带来大量的重排和重绘，而React的虚拟DOM树的比较和更新是基于JavaScript对象进行的，不会导致页面的重排和重绘。
总结起来，React虚拟DOM的原理就是：通过比较虚拟DOM树的不同，批量的更新真实的DOM树，从而提高页面的性能。
### diff算法
React Diff是React中用于更新Virtual DOM的算法它的目的是在最小化DOM操作的同时，尽可能快地更新组件。它通过比较Virtual DOM树的前后两个状态来确定哪些部分需要更新。
React Diff算法的核心思想是尽可能地复用已有的DOM节点。当Virtual DOM中的某个节点发生变化时，React会先比较该节点的属性和子节点是否有变化，如果没有变化，则直接复用该节点。如果有变化，则会在该节点的父节点下创建一个新的节点，并将新的属性和子节点赋值给该节点。
React Diff算法的具体实现有两种方式：深度优先遍历和广度优先遍历。深度优先遍历是指先比较父节点的子节点，如果子节点有变化，则递归比较子节点的子节点。广度优先遍历是指先比较同级节点，如果同级节点有变化，则递归比较子节点。
React Diff算法的优化策略包括：key属性的使用、组件的shouldComponentUpdate方法、使用Immutable.js等。其中，key属性的使用是最常用的优化策略，它可以帮助React更准确地判断哪些节点需要更新，从而减少不必要的DOM操作。

React Diff算法具有以下特点：
1. 先判断两个节点是否相等，如果相等，就不需要更新。
2. 如果两个节点类型不同，则直接替换节点。
3. 如果节点类型相同，但是节点属性不同，则更新节点属性。
4. 如果节点类型相同，但是子节点不同，则使用递归的方式进行更新。
React Diff算法的时间复杂度是O(n)，其中n为Virtual DOM树中节点的数量。

// 实例：
在React中，渲染数组时将数组的第一项移动到最后渲染的开销通常比将最后一项移动到第一项渲染的开销要大：

这是因为React使用了虚拟DOM（Virtual DOM）来进行高效的DOM操作。
当数组中的元素发生变化时，React会比较新旧虚拟DOM树的差异，并只更新需要更新的部分。
如果将数组的第一项移动到最后，React需要重新计算并比较整个数组的差异，这可能会导致更多的DOM操作。
相比之下，将最后一项移动到第一项只会影响数组的第一项和最后一项，而不会影响其他元素的位置。
因此，React只需要比较这两个元素的差异，并进行相应的DOM操作，这通常比重新计算整个数组的差异要更高效。
---
React 的 Diff 算法（也叫 Reconciliation 算法）是整个 React 性能的核心之一。
React 并不是直接使用通用的最小化差异算法（如传统的 O(n³) diff），而是基于实际 UI 特点做了大量工程化优化，使得复杂度降到 O(n)。

React 的渲染机制是：

> 每次状态更新 → 生成新的 Virtual DOM → 与旧 Virtual DOM 对比 → 计算出最小的真实 DOM 更新。

### React Diff 的核心假设（三大原则）

React 基于以下三个假设来优化 diff：

1. 同一层级的节点不同类型，则直接销毁重建
    * <div> → <span>：整个子树直接重建，不做深度比较。
2. 同类型节点，只比较属性（props）和子节点
    * <div class="a"> → <div class="b">：只更新属性，不替换节点。
3. 通过 key 标识列表项，以最小代价重用 DOM 节点
    * 列表 diff 是性能关键点。
    * 如果没有 key 或 key 重复，会引发不必要的删除和重建。

### Diff 过程的三大场景
    * 元素类型不同 → React 直接销毁旧节点及其子树，挂载新节点。无需继续比较。
    * 同类型元素 → React 比较 props 差异，只更新变动的属性。
    * 子节点（children）diff:
        - 先比较新旧列表的第一层（不递归嵌套）。
        - 使用 key 来判断元素是否可复用。
        通过 key 发现 “B” 和 “A” 都还在，只是顺序变了；结果：只移动节点，不重新创建。
        如果没有 key：→ React 会认为顺序变化是内容变化，可能会导致整项被重建。
### React Fiber 与 Diff 的关系
Fiber 不是新的 diff 算法，而是新的执行模型。

Diff 逻辑依然遵循上述启发式规则，但现在被拆分成小任务，支持：

可中断的渲染（Concurrent Mode）

优先级调度（不同更新可被分批执行）

👉 简而言之：

Fiber 负责“怎么执行 Diff”
Diff 算法负责“怎么比较节点”

## 组件
### 命令式组件
React.createElement
### 声明式组件
```
//组件名首字母大写
//类生成方式
class Header extends Component {
  render() {
    return <div>微博头部</div>;
  }
}
//函数组件：没有生命周期，不能定义自己的状态（）
//ES5生成方式
function Main() {
  return <main>微博的内容</main>;
}
//ES6生成方式
const Footer = () => {
  return <footer>微博底部</footer>;
};
```
### 受控组件
//内容可以由我们自己来控制的组件，必须要有value和onChange
### 非受控组件
//解构createRef，创建Refs并通过ref属性联系到React组件。Refs通常当组件被创建时被分配给实例变量，这样它们就能在组件中被引用。

### state
! setState在合成事件是异步的
! setState在生命周期里是异步的
! setState在定时器里面是同步的
! setState在原生js里面是同步的
### portal
（改变元素在DOM结构中的位置）
 {createPortal(<Child />, document.querySelector("html"))}             //函数，传入两个参数，第一个为组件，第二个为位置
## 类组件生命周期
React组件的生命周期分为三个阶段：挂载阶段、更新阶段和卸载阶段

挂载阶段包括以下方法：

- constructor：组件被创建时调用，用于初始化状态和绑定事件处理函数。
- getDerivedStateFromProps：在组件挂载之前和更新时调用，用于根据props更新state。
- render：根据props和state渲染组件。
- componentDidMount：组件挂载后调用，用于进行异步操作和DOM操作。

更新阶段包括以下方法：

- getDerivedStateFromProps：在组件更新时调用，用于根据props更新state。
- shouldComponentUpdate：在组件更新前调用，用于判断是否需要重新渲染组件。
- render：根据props和state渲染组件。
- componentDidUpdate：在组件更新后调用，用于进行异步操作和DOM操作。

卸载阶段包括以下方法：

- componentWillUnmount：在组件卸载前调用，用于清理定时器、取消订阅等操作。

1. componentWillReceiveProps(nextProps)
   当父组件接收到新的props时，会触发该生命周期方法。子组件的改变可能会导致父组件的props发生变化，从而触发该方法。

2. shouldComponentUpdate(nextProps, nextState)
   当父组件的props或state发生改变时，会触发该方法。该方法用于判断是否需要重新渲染组件。如果返回false，组件将不会更新。

3. componentWillUpdate(nextProps, nextState)
   在组件更新之前调用，可以在此方法中进行一些准备工作或进行一些副作用操作。

4. render()
   无论父组件的props或state是否发生改变，都会触发render方法重新渲染父组件。

5. componentDidUpdate(prevProps, prevState)
   在组件更新之后调用，可以在此方法中进行一些副作用操作，比如更新DOM或发送网络请求。

需要注意的是，以上生命周期方法中的一些在未来版本的React中可能会被废弃或改变。因此，建议在使用时查看React官方文档以获取最新信息。

### getDerivedStateFromProps
// 根据props的值去获取一个新的state
// 它前面要有一个static关键字
// 必须要return 对象或者null  如果null表示啥也不做，如果是对象就会合并之前的state

### getSnapshotBeforeUpdate
```
// 生成一个快照在更新之前
// 拿到更新前的状态给更新以后用
  getSnapshotBeforeUpdate(prevProps, prevState) {
    // 快照就是某一条记录或者某一个值，return出来被销毁周期接收
    return prevState;
  }
  
// componentDidUpdate第三个参数就是getSnapshotBeforeUpdate里面return的那个值
  componentDidUpdate(prevProps, prevState, snapshot) {
    console.log(snapshot);
  }
```

## 传参与通信
父组件向子组件通信：使用 props

子组件向父组件通信：使用 props 回调

兄弟间的传参: 通过父组件做中转

跨级组件间通信：使用 context 对象

父组件调用子组件的方法
1. 在 React 中，可以使用 `ref` 来获取子组件的实例，并调用其方法。
需要注意的是，只有在 `Child` 组件挂载到 DOM 中后，`childRef.current` 才会有值，因此要确保在调用 `handleClick` 方法之前 `Child` 组件已经挂载到了 DOM 中。
2. 函数组件中父组件调用子组件的方法  useImperativeHandle, useRef 
```
// 子组件
import React, { useImperativeHandle, useRef } from 'react';

const ChildComponent = React.forwardRef((props, ref) => {
  const childRef = useRef();

  // 子组件的方法
  const myMethod = () => {
    console.log('This is a method from ChildComponent');
  };

  // 使用useImperativeHandle来暴露方法
  useImperativeHandle(ref, () => ({
    myMethod: myMethod
  }));

  // 子组件的渲染逻辑
  return <div>I am the ChildComponent</div>;
});

export default ChildComponent;

// 父组件
import React, { useRef } from 'react';
import ChildComponent from './ChildComponent';

const ParentComponent = () => {
  const childRef = useRef(null);

  // 调用子组件的方法
  const callChildMethod = () => {
    if (childRef.current) {
      childRef.current.myMethod();
    }
  };

  return (
    <div>
      <ChildComponent ref={childRef} />
      <button onClick={callChildMethod}>Call Child Method</button>
    </div>
  );
};

export default ParentComponent;
```

# hooks
## 核心
1. 基于规则的工作机制：React 通过一套严格的规则来管理 Hooks 的使用，例如必须在**函数组件顶层调用 Hook，不能在循环、条件或嵌套函数中调用。**

2. 组件状态与生命周期的函数化：Hooks 将组件状态和生命周期行为抽象为独立的、可复用的函数，使复杂逻辑易于管理和测试。

3. 对函数式编程思想的运用：Hooks 通过纯函数和闭包来处理状态和副作用，鼓励使用不可变数据和函数组合，提升代码的可预测性和简洁性。

## 实现原理
### useState实现原理

### useEffect实现原理

### useRef实现原理

### useMemo实现原理


##  Hooks分类
### 页面相关Hooks
useState（改变状态是异步的）

useContext（组件通信）

useLayoutEffect（同步执行副作用）

useEffect（处理副作用）

useMemo

useCallback（记忆函数）

useEvent（缓存事件处理函数）

useRef

useImperativeHandle（子组件暴露内部方法给父组件）

### 路由相关
useHistory

useParams

useRouteMatch

useLocation

### redux相关

