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

### react 是如何实现页面的快速响应？
原因：在浏览器中，每秒渲染约 60 帧，也就是每一帧只能使用大约 16.6ms 来完成所有工作，包括 JS 执行、样式计算、布局和绘制。而 JS 线程和渲染线程是互斥的，如果 JS 执行时间过长（比如一次性渲染大量组件），就会阻塞渲染线程，导致页面掉帧或者卡顿。

方案：React 18 引入了时间切片（Time Slicing）机制。它的核心思想是：把原本同步、不可中断的更新过程拆分成多个小任务，分布在多帧中执行，并且在每一帧中预留一部分时间给浏览器渲染。将同步的更新变为可中断的异步更新。

结果：
✅ 避免长时间占用主线程，防止页面卡顿

✅ 优先处理用户交互等高优任务，提升响应性

### 什么是fiber
Fiber 是 React 16 引入的底层架构，用于 **增量渲染和调度 UI 更新**。

* 它将渲染任务拆成小块，可中断、恢复，避免复杂更新阻塞主线程。
* 支持优先级调度，高优先级任务（如用户输入）先执行，低优先级任务延后。
* 每个组件对应一个 Fiber 节点，记录状态、子节点、兄弟节点等，形成链表树结构，用于高效更新。

### React Fiber 架构解决了什么问题？
React Fiber 架构主要解决了 **React 之前同步渲染带来的性能和响应性问题**。具体来说：

1. **阻塞主线程问题**

   * 以前 React 更新是同步的，遇到大规模或复杂组件树更新时，会导致页面卡顿，用户交互被阻塞。
   * Fiber 将渲染拆分成小任务，可以暂停和恢复，避免长时间占用主线程。

2. **缺乏优先级调度**

   * 以前所有更新一视同仁，无法区分重要性。
   * Fiber 支持不同优先级的更新，高优先级任务（如输入、动画）先执行，低优先级任务（如后台渲染）可以延后。

3. **无法增量渲染**

   * 旧版 React 需要一次性完成整个更新，导致大组件树更新时性能下降。
   * Fiber 支持增量渲染（分时间片完成），保证 UI 响应流畅。

一句话总结：

> Fiber 架构让 React 的界面更新 **可中断、可分片、按优先级调度**，解决了大规模更新卡顿和响应慢的问题。

### fiber工作原理
React Fiber 是一种新的协调引擎，用来提高 React 应用的性能和响应能力。
Fiber 通过增量渲染、可中断与恢复、链表结构和优先级调度等机制，使得 React 可以更灵活地处理大量更新和复杂组件树。

工作原理：
调度：Fiber 引入了新的调度机制，允许 React 根据任务的优先级来调度任务。React 会根据任务的紧急程度将任务放入不同的队列中，并按照队列的顺序执行任务。

渲染：在渲染阶段，React 会遍历组件树，并构建一个 Fiber 树。Fiber 树中的每个节点代表一个组件，并包含组件的状态、属性等信息。

更新：当组件的状态或属性发生变化时，React 会触发更新。Fiber 会根据变化的类型和优先级来决定如何更新组件。

提交：在更新阶段完成后，React 会将 Fiber 树转换为实际的 DOM 树，并提交给浏览器进行渲染。

#### fiber结构
每个Fiber节点有个对应的React element
```
// 指向父级Fiber节点
this.return = null;
// 指向子Fiber节点
this.child = null;
// 指向右边第一个兄弟Fiber节点
this.sibling = null;
```

### fiber 是如何实现时间切片的？
React Fiber 实现时间切片主要依赖于两个核心功能：任务分割和任务优先级。

任务分割是指将一个大的渲染任务切割成多个小任务，每个小任务只负责一小部分 DOM 更新。React Fiber 使用 Fiber 节点之间的父子关系，将一个组件树分割成多个”片段“，每个“片段”内部是一颗 Fiber 子树，多个“片段”之间可以交错执行，实现时间切片。

任务优先级是指 React Fiber 提供了一套基于优先级的算法来决定哪些任务应该先执行，哪些任务可以放到后面执行。React Fiber 将任务分成多个优先级级别，较高优先级的任务在进行渲染时会优先进行，从而确保应用程序的响应性和性能。

1. React Fiber 会将渲染任务划分成多个小任务，每个小任务一般只负责一小部分 DOM 更新。
2. React Fiber 将这些小任务保存到任务队列中，并按照优先级进行排序和调度。
3. 当浏览器处于空闲状态时，React Fiber 会从任务队列中取出一个高优先级的任务并执行，直到任务完成或者时间片用完。
4. 如果任务完成，则将结果提交到 DOM 树上并开始下一个任务。如果时间片用完，则将任务挂起，并将未完成的工作保存到 Fiber 树中，返回控制权给浏览器。
5. 当浏览器再次处于空闲状态时，React Fiber 会再次从任务队列中取出未完成的任务并继续执行，直到所有任务完成。

### 👉「Fiber 是如何终止（中断）渲染任务的？」

React 通过“时间切片（Time Slicing）”机制，在每个 Fiber 节点的执行过程中主动检查是否超时或有更高优先级任务，一旦发现，就主动中止当前任务，保存执行状态，稍后再恢复。

在 Fiber 架构中，渲染（Reconciliation）不再是递归调用，而是一个循环任务：
任务终止（中断）的触发点：shouldYield()

当 shouldYield() 返回 true：
表示主线程即将被浏览器用来渲染页面或响应用户操作

React 立即停止当前循环

保留当前 Fiber 的执行状态（nextUnitOfWork）

退出到浏览器主线程

等浏览器空闲后（下一帧），再继续执行上次中断的任务。

### 为什么 React 可以“中断”而不会出错？

因为：

Fiber 是链表结构（非递归调用栈）

每个 Fiber 节点保存了完整的执行上下文（props、state、return 指针等）

所以中断后可以从当前节点恢复，不依赖于 JS 调用栈

这正是 Fiber 架构最大的设计亮点之一。

### 具体做了什么事情
### fiber具体场景的执行优先级

Fiber 架构中的优先级机制，其核心目标是**确保高优先级的用户交互（如输入、点击）能够及时响应**，而低优先级的任务（如数据获取、离线渲染）可以适当让步。

#### 具体场景与优先级对应

React 内部定义了不同优先级，以下是从高到低的具体场景：

1.  **同步优先级**
    *   **场景**：`flushSync` 中的更新、输入框的防抖更新。
    *   **行为**：立即执行，不可中断。类似于旧版栈调和，用于需要与浏览器状态严格同步的场景。

2.  **最高优先级**
    *   **场景**：离散的用户输入，如**点击、按键**。
    *   **行为**：需要立即反馈，中断任何正在进行中的低优先级渲染，以保持应用响应灵敏。

3.  **高优先级**
    *   **场景**：**连续**的用户交互，如**鼠标悬停、滚动**。
    *   **行为**：需要快速响应，但如果一个最高优先级的更新到来，它会被中断。

4.  **普通优先级**
    *   **场景**：**最常见的场景**，如正常的 `setState` 更新。
    *   **行为**：这是默认的更新优先级。会被更高优先级的更新中断。

5.  **低优先级**
    *   **场景**：**延迟非紧急的更新**，如切换到新的页面、加载更多数据。
    *   **行为**：会为更高优先级的任务让步，可能会被多次中断和重启。

6.  **离线优先级**
    *   **场景**：**在后台预渲染**当前不可见的内容（如离屏的Tab页）。
    *   **行为**：优先级最低，永远不会阻塞用户交互。

#### 工作机制

React 使用**优先级调度**模型。当一个高优先级任务到达时（如用户点击）：
*   **中断**：React 会中断当前正在进行的低优先级渲染。
*   **处理**：先完成高优先级任务的整个渲染和提交。
*   **恢复/重启**：然后，要么恢复之前中断的低优先级任务，要么直接丢弃并重新开始。

**总结**：Fiber 优先级的意义在于，让 React 能够像操作系统调度进程一样，**智能地分配计算资源**，始终保证用户的紧急操作得到第一时间处理，从而提升用户体验的流畅度。

### 怎么进行任务终止的

关键实现原理
Fiber 节点的结构：每个 Fiber 节点都是一个工作单元，包含了组件的类型、状态、DOM 信息，以及关键的指针（child, sibling, return），使得遍历可以暂停和恢复。

workLoopConcurrent：React 的并发工作循环。它在处理每个 Fiber 节点后，会检查是否还有剩余时间（通过 shouldYield 函数）。如果没有时间，或有更高优先级的更新到达，React 就会将控制权交还浏览器，并记录下当前已完成和下一个待处理的 Fiber 节点。

双缓存 Fiber 树：React 同时维护两棵 Fiber 树：

Current Tree：当前屏幕上显示内容对应的树。

WorkInProgress Tree：正在内存中构建的树，用于下一次渲染。

一个低优先级的 WorkInProgress Tree 构建被中断时，React 会直接丢弃这整棵未完成的树。当再次处理该任务时，它会基于最新的 Current Tree 和最新的 state，重新开始构建一棵新的 WorkInProgress Tree。

总结：Fiber 通过将渲染分解为可度量的小单元、在单元之间检查中断需求、以及使用双缓存树来安全地丢弃旧工作，实现了任务的终止与重启，从而优先响应用户交互。


#### React Hooks 的使用规则和底层原理？

##### ✅ React Hooks 的使用规则

1.  **只在最顶层调用 Hook**：不能在循环、条件或嵌套函数中调用 Hook。
2.  **只在 React 函数中调用 Hook**：在 React 的函数组件或自定义 Hook 中调用。

---

##### 底层原理（基于 Fiber 架构）

React 通过在内部维护一个 **“记忆状态”链表** 来跟踪 Hooks。

*   **Fiber 节点**：每个组件对应一个 Fiber 节点，它存储了组件的状态、副作用等信息。
*   **Hooks 链表**：在组件首次渲染时，每当调用一个 Hook（如 `useState`, `useEffect`），React 就会将其按执行顺序添加到该组件 Fiber 节点的 **Hooks 链表** 中。
*   **顺序的重要性**：后续渲染时，React 会**严格依赖这个链表的顺序**来依次读取每个 Hook 对应的状态。如果顺序被打乱（如在条件语句中调用 Hook），React 就无法正确地将状态与 Hook 关联起来，导致 bug。

**以 `useState` 为例**：
*   首次渲染：调用 `useState(initialValue)`，React 会创建一个 Hook 对象（包含 `state` 和 `queue`），将其放入链表，并返回 `[state, setState]`。
*   后续渲染：再次调用 `useState`，React 不会使用初始值，而是顺着链表找到对应的 Hook 对象，返回当前最新的状态。

**`useEffect` 的原理**：
*   它的依赖项数组会被存储到 Hook 对象里。每次渲染后，React 会将新的依赖项与上一次的进行**浅比较**。如果不同，就将副作用函数标记并在合适的时机（after paint）执行。

**核心**：Hooks 的本质是**将组件的状态和副作用与一个顺序确定的链表节点绑定**。规则保证了这种绑定的稳定性。



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

#### ✅ React 的虚拟 DOM 是什么？为什么要用它？

##### 虚拟DOM是什么？

虚拟DOM是一个**轻量的JavaScript对象**，它是真实DOM的抽象表示。React用它来描述我们希望页面的样子。

*   **结构**：本质上是一个嵌套的JS对象（React Element树），包含了标签名、属性、子元素等信息。
*   **创建**：在组件渲染时，`render` 方法会返回一个新的虚拟DOM树。

---

##### 为什么要用虚拟DOM？（核心价值）

它的首要目的**不是更快地更新DOM**，而是为了实现更**声明式**和**可预测**的编程模型，并在此基础上提供不错的性能。其价值体现在两个层面：

**1. 声明式与抽象层**
*   **解决了什么**：免去开发者手动进行繁琐、易错的DOM操作（如 `appendChild`, `removeChild`）。
*   **如何实现**：你只需要**声明**视图应该是什么状态，React负责通过虚拟DOM将状态映射到实际的DOM更新上。这极大地提升了开发效率和代码可维护性。

**2. 高效的协调算法**
*   **解决了什么**：在声明式编程下，任何状态变化都会触发整棵组件树的重新渲染，直接操作真实DOM代价高昂。
*   **如何实现**：
    *   **Diffing**：当状态变化，生成新的虚拟DOM树后，React会将新树与旧树进行**比较（Diff）**，精确找出发生变化的最小节点集合。
    *   **批量更新**：React会将计算出的所有DOM更新**批量处理**，在一次重排/重绘周期中完成，避免了不必要的布局计算。

**总结**：虚拟DOM的核心价值是提供了 **“声明式编程 + 高效的差异化更新”** 的组合。它通过 **JS运算（Diffing）** 的成本来换取**减少昂贵DOM操作**的成本，并在复杂的应用交互中保持性能的稳定和可预测。
### 虚拟DOM 一定会比直接操作 真实 DOM 快吗
1. 虚拟DOM不一定会比操作原生DOM更快。(首次渲染需要创建虚拟DOM)
2. 虚拟DOM的优势在于节点进行改动的时候尽量减少开销
3. 框架的本质是提升开发效率，让我们的注意力更集中于数据

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

#### ✅ 组件通信有哪些方式？

React 组件通信方式根据组件关系不同，主要有以下几种：

##### 1. 父子组件通信
*   **父 -> 子**：通过 **`props`** 传递数据和函数。
*   **子 -> 父**：父组件通过 `props` 传递一个**回调函数**给子组件，子组件调用该函数向父组件传递数据。

##### 2. 兄弟组件通信
*   **状态提升**：将共享状态提升至最近的公共父组件，由父组件通过 `props` 分发和管理。这是 React 最推荐的基本方式。

##### 3. 深层级/任意组件通信
当组件层级很深或无关时，上述方式会变得繁琐，此时有更优方案：

*   **Context API**：创建一个 Context，由顶层 `Provider` 提供数据，底层任何子组件通过 `useContext` Hook 消费数据。适用于**全局、不频繁更新**的数据（如主题、用户信息）。

*   **状态管理库**：如 **Redux, Zustand, MobX**。
    *   创建一个**全局 Store**，组件通过 `useSelector` 等 API 订阅所需数据。
    *   通过 `dispatch` 动作或直接调用方法来更新状态。
    *   适用于**复杂应用状态**或**高频更新**的场景。

##### 4. 其他方式
*   **事件总线**：一个全局事件系统（如 `EventEmitter`），组件可订阅和发布事件。但它在 React 中不被提倡，因为它破坏了数据流 predictability，且易导致内存泄漏。
*   **Ref 传递**：父组件通过 `ref` 获取子组件实例（仅适用于 Class 组件）或调用子组件的方法（使用 `useImperativeHandle` Hook）。

**总结**：优先考虑 `props` 和**状态提升**，复杂场景下使用 **Context** 或**状态管理库**。选择取决于数据流动的复杂度和更新频率。
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

### 为什么不能在循环、条件或嵌套函数中调用 Hooks？
核心原因是 React 依赖 Hook 的调用顺序 来正确关联状态和对应的 Hook。

React 内部为每个组件维护了一个 Hook 链表，每次调用 Hook 时，React 就按顺序移动到链表的下一个节点。在重新渲染时，React 依赖完全一致的调用顺序，才能把正确的状态返回给对应的 Hook。

如果我们在条件或循环中使用 Hook，就会破坏这种顺序一致性。比如第一次渲染时条件为真调用了某个 Hook，第二次渲染时条件为假跳过了它，这样后面所有的 Hook 都会错位，导致状态混乱。
```
// ❌ 危险的代码
if (condition) {
  useState('first');  // 第一次渲染：这个 Hook 存在
}                     // 第二次渲染：condition 为 false，这个 Hook 被跳过
useState('second');   // 现在这个 Hook 拿到了 'first' 的状态！
```

#### ✅ React Hooks： 解释 useEffect 和 useLayoutEffect 的区别。

`useEffect` 和 `useLayoutEffect` 的唯一区别在于它们**执行的时机**。

*   **`useEffect`**：是 **异步** 的。它会在浏览器完成本次的**绘制与布局之后**、下一次绘制之前被触发。它**不会阻塞**浏览器的渲染更新。

*   **`useLayoutEffect`**：是 **同步** 的。它会在所有的DOM变更之后、但浏览器**进行绘制之前**同步执行。它会**阻塞**浏览器的绘制。

### 核心影响与使用场景

*   **`useEffect`**：适用于**绝大多数副作用**，如数据获取、设置订阅、手动更改非布局相关的DOM等。这是默认的选择。

*   **`useLayoutEffect`**：适用于需要**同步计算样式或布局**的场景。如果你需要在一个DOM元素被绘制到屏幕之前，根据其新的样式或尺寸进行额外的读取或修改（比如测量元素尺寸、然后设置一个tooltip的位置），就必须使用 `useLayoutEffect`，这样可以避免用户看到闪烁的中间状态。

**简单比喻**：`useEffect` 是“事后诸葛亮”，而 `useLayoutEffect` 是“防患于未然”。
#### 在 useEffect 中清理副作用的必要性是什么？

在 `useEffect` 中清理副作用是 **防止内存泄漏、保证应用稳定性和正确性** 的关键手段。主要有三个场景：

1.  **防止内存泄漏**：当组件卸载时，必须清理在 `useEffect` 中创建的、会持续存在的资源。否则，这些资源会一直占用内存。
    *   **场景**：定时器 (`setInterval`, `setTimeout`)、事件监听器 (`addEventListener`)、WebSocket 连接。
    *   **清理**：在清理函数中清除定时器、移除事件监听、关闭连接。

2.  **避免无效更新**：当依赖项变化导致 `useEffect` 重新执行时，需要先清理上一次副作用造成的影响。
    *   **场景**：一个根据 `props.id` 获取数据的组件。如果 `id` 变化，发起了新的数据请求，应该取消上一次未完成的请求，防止旧数据覆盖新数据。

3.  **维持数据一致性**：在异步操作中，如果组件已卸载，清理函数可以阻止对已卸载组件状态的更新。
    *   **场景**：一个异步数据获取操作。如果在响应返回前组件卸载了，清理函数可以设置一个标志位，阻止后续的 `setState` 操作，避免在未挂载的组件上更新状态而报错。

**核心思想**：每次渲染都是一个独立的“版本”，清理函数的作用就是**清除属于上一个“版本”的、已经过时的影响**，确保当前渲染的副作用是干净且有效的。
#### ✅ useMemo 和 useCallback 的使用场景和陷阱是什么？

`useMemo` 和 `useCallback` 都是用于性能优化的Hook，其核心都是**通过缓存来避免不必要的重复计算**。

##### 使用场景

*   **`useMemo`**：用于**缓存计算代价高昂的结果**。
    *   **场景**：对大型数据集进行筛选、排序、转换等复杂计算。避免每次渲染都重复执行。

*   **`useCallback`**：用于**缓存函数本身**。
    *   **场景**：当将函数作为props传递给被 `React.memo` 优化的子组件时，缓存函数可以避免子组件因父组件渲染而进行不必要的重渲染。

##### 共同陷阱

1.  **不必要的优化**：这是最大的陷阱。缓存本身需要内存和计算成本。如果计算不昂贵或函数不作为props传递，使用它们反而可能**降低性能**。应该先测量，再优化。

2.  **依赖项数组错误**：如果依赖项数组填写不完整，会使用过期的缓存值，导致难以追踪的bug。

3.  **语义混淆**：`useCallback(fn, deps)` 等价于 `useMemo(() => fn, deps)`。它缓存的是**函数引用**，并不会阻止函数内部的逻辑在每次调用时执行。

**最佳实践**：默认不要使用。只有在遇到明确的性能问题（如渲染卡顿、子组件频繁无意义重渲染）时，才在测量后有针对性地使用。



## hook是什么时候调用

## 实现原理
### useState实现原理

useState 的原理核心是：基于函数组件的渲染顺序，通过链表结构来跟踪和管理状态。React 为每个组件维护一个状态链表，依靠 Hook 的调用顺序来正确关联状态。

第一，数据结构层面：
React 为每个函数组件在 Fiber 节点中维护一个 Hook 链表。每个 useState 调用对应链表中的一个节点，包含状态值和更新队列。

第二，执行流程层面：

首次渲染：调用 useState 时，React 创建新的 Hook 节点，存储初始状态

更新渲染：调用 useState 时，React 按顺序遍历链表，返回之前保存的状态

调用 setState：将更新加入队列，调度重新渲染

第三，更新机制层面：
当调用 setState 时，React：

创建更新对象加入队列

调度重新渲染

重新执行组件函数

按顺序从链表中读取最新状态

返回新的状态值触发更新
### useEffect实现原理

useEffect 的工作原理核心是：在组件渲染完成后调度副作用函数，并通过依赖数组进行精确控制。它让函数组件能够执行副作用操作，类似于类组件的生命周期方法。

第一，声明阶段 - 在组件渲染时：

useEffect 在组件函数执行时被调用，但不会立即执行副作用

React 只是记录这个 effect 的信息（回调函数、依赖数组、清理函数）

第二，提交阶段 - 在 DOM 更新后：

React 完成 DOM 更新后，将所有的 effects 加入任务队列

这些 effects 会在浏览器完成绘制后异步执行

第三，执行阶段 - 异步执行副作用：

React 遍历 effects 链表，执行每个 effect 的创建函数

如果 effect 返回了清理函数，React 会保存它供后续使用

第四，清理阶段 - 在依赖变化或卸载时：

在下次 effect 执行前，会先执行上一次的清理函数

组件卸载时，所有 effect 的清理函数都会执行

关键点：useEffect 是异步的，不会阻塞浏览器绘制，这与 useLayoutEffect 的同步执行形成对比。"

### useRef实现原理

### useMemo实现原理

### setState原理

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

### React v18中的hooks
useSyncExternalStore：是一个推荐用于读取和订阅外部数据源的 hook（如 Redux、Zustand、自定义全局状态）
useTransition：作用：标记“非紧急更新”，用于优化交互体验，把一些状态更新标记为“过渡任务（Transition）”，React 可以打断这些任务，提高响应速度，常用于：搜索过滤、大数据渲染、切换选项卡等
useDeferredValue：对某个值做“延迟处理”，避免每次变化都立刻触发重渲染，常用于搜索建议、输入联想
useInsertionEffect：用于在 DOM 更新前插入样式（CSS-in-JS 库使用），执行时机比 useLayoutEffect 更早
useId() 作用：生成在客户端与服务端一致的稳定唯一 ID，用于可访问性标签或表单元素 id/for 绑定。

### redux相关

### react 18 19新特性
React 18 引入了**并发渲染**，**不再是同步阻塞渲染\React 可以“中断”和“恢复”渲染，提高流畅度
**自动批处理**、以前只有事件内 setState 会一起批处理，现在Promise、setTimeout、异步请求中也会自动批处理
Suspense SSR 和新根 API，这些提升了性能和开发体验。

React 19 是一次架构级革新，它通过 Server Components 和 Actions API 把 React 从“前端渲染框架”提升为“前后端一体化框架”，实现减少 JS 体积、直连数据库、自动表单处理等功能，是未来 React 应用的主流模式。


## ✔ React 18 的主要新特性

* **自动批处理（Automatic Batching）**：更新不仅限于 React 事件处理器内部，Promise、setTimeout、原生事件处理器内的多次 `setState` 也会被合并为一次渲染，从而减少重渲染次数。 ([mtechzilla.com][1])
* **并发特性初步支持（Concurrent Features）**：引入 “转场（Transitions）”的概念，用来区分紧急更新（如用户输入）与可中断/延迟更新（如页面切换）。例如 `startTransition`、`useDeferredValue`。 ([AppSignal Blog][2])
* **新的客户端／服务端渲染 API**：例如 `ReactDOM.createRoot`、`hydrateRoot` 取代旧的 `ReactDOM.render`、`ReactDOM.hydrate`。服务端支持 `renderToPipeableStream`、`renderToReadableStream` 等流式渲染。 ([阿里云开发者社区][3])
* **对 `Suspense` 的增强**：在服务端也支持 `Suspense`，使慢组件不会阻塞整个页面渲染。 ([freecodecamp.org][4])
* **严格模式增强（Strict Mode Behaviors）**：用于开发模式下模拟组件多次挂载／卸载、增强对并发模式适配的能力。 ([React][5])

---

## ✔ React 19 的主要新特性

（这是更前沿的版本，可凸显你对新技术趋势的关注）

* **Actions API**：引入用于异步操作（如表单提交、数据变更）的 Actions 概念，支持自动管理 pending 状态、乐观更新（optimistic updates）、错误回退。 ([React][6])
* **新 Hook：`useActionState`、`useOptimistic`、`useFormStatus`** 等：分别用于管理 Action 状态、乐观 UI、表单提交状态。 ([React][6])
* **`use` API**：允许在 render 中读取 Promise 或资源，配合 `Suspense` 使用。 ([React][6])
* **`ref` 作为 prop 支持**：在函数组件中可以直接把 `ref` 当做 prop 使用，无需 `forwardRef`。 ([React][6])
* **静态 API（Static Site Generation）增强**：新增 `react-dom/static` 的 `prerender` 与 `prerenderToNodeStream`，用于静态预渲染和流式生成。 ([React][7])
* **开发者体验 &型别支持增强**：更强的 TypeScript 支持、更好的错误报错（尤其 hydration mismatch）、降低第三方库依赖。 ([OneDev][8])

---

## 🤔 面试中可能追问的问题 &你如何答

* **“Actions 和 Hooks 是如何改变你写表单/数据操作的方式？”**
  可以说明：以前要手动管理 loading、error、reset 等状态，现在 Actions／useActionState 等 API 提供了更统一、更少 boilerplate 的方式。
* **“你们项目如果升级到 React 19，会带来什么好处？有什么风险？”**
  好处包括：表单/数据操作更简洁、并发体验更好、SSR/静态生成更强。风险包括：生态库兼容性、团队熟悉度、迁移成本。
* **“Concurrent Rendering 默认为 React 19 那意味着什么？”**
  可以说明：UI 响应性能更好，但也意味着组件必须更健壮（例如避免副作用滞留、确保可中断操作安全）。


### 如何监听路由变化？
基于useEffect，和useLocation，通过useEffect监听当前location的变化
```
const location = useLocation();
useEffect(() => {
  //记录路径
}, [location]);
```

### react-router 页面跳转时，是如何传递下一个页面参数的？
1. 路由参数（Route Parameters）：（如 /users/:id）props.match.params获取
2. 查询参数（Query Parameters）：如 /users?id=123&name=John props.location.search
3. 路由状态（Route State）：props.location.state 
路由状态仅在通过 history.push 或 history.replace 导航到新页面时才可用。如果用户通过浏览器的前进/后退按钮进行导航，或者直接输入 URL 地址访问页面，路由状态将不会被保留。
4. 上下文（Context）：上下文功能共享路由相关的数据。

### 父组件调用子组件的方法
函数组件、Hook组件
在 React 中父组件不能直接调用子组件的方法，但我们可以通过 ref 配合 forwardRef 和 useImperativeHandle 在函数组件中显式暴露子组件方法给父组件调用。

### useRef、ref、forwardsRef 的区别是什么
1. ref：ref 是 React 中用于访问 DOM 元素或组件实例的方法。在函数组件中，可以使用 useRef Hook 来创建一个 ref 对象，然后将其传递给需要引用的元素或组件。在类组件中，可以直接在类中定义 ref 属性，并将其设置为元素或组件的实例。

2. useRef：useRef 是 React 中的 Hook，用于创建一个 ref 对象，并在组件生命周期内保持其不变。useRef 可以用于访问 DOM 元素或组件实例，并且在每次渲染时都会返回同一个 ref 对象。通常情况下，useRef 更适合用于存储不需要触发重新渲染的值，例如定时器的 ID 或者其他副作用。

3. forwardRef：forwardRef 是一个高阶组件，用于将 ref 属性转发给其子组件。通常情况下，如果一个组件本身并不需要使用 ref 属性，但是其子组件需要使用 ref 属性，那么可以使用 forwardRef 来传递 ref 属性。forwardRef 接受一个函数作为参数，并将 ref 对象作为第二个参数传递给该函数，然后返回一个新的组件，该组件接受 ref 属性并将其传递给子组件。

简而言之，ref 是 React 中访问 DOM 元素或组件实例的方法，useRef 是一个 Hook，用于创建并保持一个不变的 ref 对象，forwardRef 是一个高阶组件，用于传递 ref 属性给子组件。

### forwardRef作用是什么？
forwardRef 是 React 提供的一个高阶函数，它可以让你在函数组件中访问子组件的 ref，并把该 ref 传递给子组件。

### createContext 和 useContext 有什么区别， 是做什么用的
createContext和useContext是React中用于处理上下文（Context）的两个钩子函数，它们用于在组件之间共享数据。

createContext：createContext用于创建一个上下文对象，并指定初始值。它返回一个包含Provider和Consumer组件的对象。

useContext：useContext用于在函数组件中访问上下文的值。它接受一个上下文对象作为参数，并返回当前上下文的值。

### 遍历渲染节点列表， 为什么要加 key 

key 是用来唯一标识列表中的每个节点，帮助 React 在进行 Diff 算法时准确判断哪些元素被新增、删除或移动，从而实现高效更新，避免不必要的重新渲染。

为什么不能用 index 作为 key
使用 index 时，如果列表发生插入/删除操作，index 会改变，导致 React 错误判断节点身份

## 合成事件
React 合成事件 = 统一封装的事件对象 + 顶层事件委托 + 批量更新优化机制

React 的合成事件是 React 在原生事件之上实现的一套统一事件系统。
它通过事件委托，将所有事件统一绑定到根节点，然后在事件触发时根据虚拟 DOM 找到对应组件并触发回调。
合成事件对象（SyntheticEvent）对不同浏览器进行了兼容性封装，并且支持事件池和批量更新机制，从而提高应用性能和可控性。
React17 之后，事件委托改为绑定在应用根节点，以增强与原生事件的共存能力。

## 获取页面静态资源
const resources = window.performance.getEntriesByType('resource');

## react lazy import 实现懒加载的原理是什么？
React.lazy 是基于 ES 动态 import 实现的，通过 webpack 打包拆分代码，只有在组件真正被渲染时才加载对应 chunk。
React.lazy 将 import 返回的 Promise 包装为一个特殊的 Lazy Fiber 节点，在渲染过程中如果遇到 pending 状态，就会让出控制权，进入 Suspense fallback 界面。
当 Promise resolve 后，React 再次渲染真实组件，实现懒加载和异步渲染。


## Portals 作用是什么， 有哪些使用场景？
React Portals 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的方式。

React Portals 的作用：
父子结构逃逸, 样式继承独立, 事件冒泡正常

场景：模态框、通知、浮动菜单、全屏组件
```
class Modal extends React.Component {
  render() {
    // 使用 ReactDOM.createPortal 将子元素渲染到 modal-root 中
    return ReactDOM.createPortal(
      // 任何有效的 React 孩子元素
      this.props.children,
      // 一个 DOM 元素
      document.getElementById("modal-root")
    );
  }
}
```

## 是如何处理组件更新和渲染的？

React的更新渲染可以概括为三个核心阶段：

首先，触发更新。当组件的state、props或消费的Context发生变化时，就会触发更新流程。

然后进入渲染阶段，这是纯JS计算的过程。React会重新执行组件函数，生成新的虚拟DOM树，然后通过高效的Diff算法对比新旧两棵树的差异。这里React会尽量复用已有的DOM节点，比如列表渲染时通过key来跟踪元素 identity。

最后是提交阶段，React将计算出的差异一次性批量更新到真实DOM上。这个阶段还会按顺序执行相关的生命周期和Effects：先是useLayoutEffect同步执行，然后是useEffect异步执行。

在实际项目中，我们经常会用React.memo、useCallback这些API来优化不必要的子组件重渲染，提升应用性能。

整体来说，React通过这种虚拟DOM和差异对比的机制，让我们可以用声明式的方式开发，同时保证了不错的运行时性能。

更新和渲染流程：
- 当组件的 state 或者 props 发生变化时，React 会将新的 props 和 state 比较之前的，根据比较结果决定是否进行更新。
- 如果 shouldComponentUpdate、PureComponent 或 React.memo 表示不需要更新，React 将不会进行更新。
- 如果需要更新，React 会调用 render 方法以及相关的生命周期方法或 Hooks，这个过程会创建一个虚拟 DOM 树。
- React 之后会对比新的虚拟 DOM 树与上一次更新时的虚拟 DOM 树，通过 DOM diffing 算法判断在哪进行实际的 DOM 更新。
- 应用必要的 DOM 更新到实际的 DOM 树上，如果有必要，调用 getSnapshotBeforeUpdate 和 componentDidUpdate 方法。

## memo 和 useMemo
React.memo 的作用是优化函数组件的渲染性能，它可以比较组件的 props 和 state 是否发生变化，如果没有变化，就不会重新渲染。

useMemo 的作用是缓存计算结果，从而避免重复计算，它接受一个计算函数和一个依赖项数组，当依赖项发生变化时，计算函数就会重新计算，返回一个新的结果，否则就会使用之前的缓存结果。

## [React] 如何实现路由守卫
`<Route>` 组件：可以在路由配置中定义特定路由的守卫逻辑。例如，可以设置 render 属性或者 component 属性来渲染组件，并在渲染前进行守卫逻辑的判断。
```
  const requireAuth = (Component) => {
    return () => {
      if (isAuthenticated()) {
        return <Component />;
      } else {
        history.push("/login");
        return null;
      }
    };
  };
<Route path="/home" render={requireAuth(Home)} />
```

## 构建组件的方式
Class Components（类组件）
Function Components（函数组件）
Higher-Order Components（高阶组件）
React.cloneElement
React.createElement

## useEffect的第二个参数，如何判断依赖是否发生变化
React 会保存上一次的依赖数组，每次渲染后逐个用 Object.is 比较当前依赖和上一次是否一致。只要其中任意一个依赖的引用或值发生变化，effect 就会被重新执行。对象、数组、函数必须保持引用稳定，否则每次都会触发。

React 实际使用的是 Object.is 进行比较，它比 === 更准确，能处理 NaN 和 -0 等边界情况。

Object.is() 方法是严格相等比较，而 "===" 操作符也是严格相等比较，但 "==" 操作符是相等比较。

严格相等比较（===）要求比较的两个值在类型和值上完全相同才会返回 true。
相等比较（==）会进行类型转换，将两个值转换为相同类型后再进行比较。
Object.is() 方法对于一些特殊的值比较更准确：

对于 NaN 和 NaN 的比较，Object.is(NaN, NaN) 返回 true，而 NaN === NaN 返回 false。
对于 +0 和 -0 的比较，Object.is(+0, -0) 返回 false，而 +0 === -0 返回 true。


## react优化
React优化的理解主要围绕 减少不必要的渲染 和 优化资源加载 两个核心。

首先，在减少渲染方面，我会根据场景使用 React.memo、useMemo 和 useCallback 来建立缓存，确保组件和计算只在依赖变化时更新。同时，我会合理设计状态结构，避免状态提升过高。

其次，在资源加载方面，我会用 React.lazy 做组件级的代码分割，减少首屏资源体积。

此外，像给列表加 key、保持数据不可变这些最佳实践，也是在日常开发中必须遵循的。

## dom diff 的原理
