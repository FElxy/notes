https://juejin.cn/post/7493840743367344168

https://www.developers.pub/wiki/1065322

## nextTick 作用是什么， 原理是什么
作用是确保DOM更新后再执行的操作，
 - Vue 是异步更新 DOM 的。当在代码中修改了数据，Vue 不会立即更新 DOM，而是将这些更新操作放入一个队列中，等待下一个“tick”（事件循环的一个周期）再统一进行 DOM 更新。

原理：利用事件循环

实现：
Vue 内部维护了一个异步任务队列，用于存储nextTick的回调函数。当调用nextTick时，回调函数会被添加到这个队列中。
Vue 在更新 DOM 后，会检查这个异步任务队列是否为空。如果不为空，会取出队列中的第一个任务并执行它。
这样就保证了在 DOM 更新完成后，nextTick的回调函数能够按照调用的顺序依次执行。

## 介绍一下 component 动态组件
动态组件使用特殊的<component>标签结合is属性来实现。is属性可以接受一个字符串或变量，用于指定要渲染的组件名称或组件选项对象。Vue 会根据is属性的值来动态地加载和渲染相应的组件。

优势：
- 灵活性：可以根据不同的业务逻辑和用户交互动态地切换组件，提高应用的灵活性和可维护性。
- 代码复用：可以在多个地方使用相同的动态组件机制，减少重复代码。
- 性能优化：只在需要的时候加载和渲染特定的组件，可以提高应用的性能。

## 介绍一下 teleport 内置组件
提供了一种将组件的模板内容渲染到指定 DOM 节点位置的方式，而不是在组件自身的位置渲染。

原理：
Teleport 是在虚拟 DOM 层面引入的一种特殊节点类型，它在逻辑上仍属于组件树的一部分，但其真实 DOM 会被渲染到另一个挂载点。Vue 在 Renderer 阶段把子节点挂载到 to 指向的 DOM 容器中。

Teleport 是 Vue 3 在 Renderer 层面引入的特殊节点，通过 mount 和 move 实现 DOM 的跨容器渲染。在 SSR 中，Teleport 内容会在服务端正常输出，但 hydration 时会被重新挂载到目标容器，保证一致性。
当与 Suspense 或异步渲染结合时，Teleport 由 scheduler 调度，分配优先级，确保 DOM 移动在正确时机发生，不影响父组件渲染顺序。
本质上，它体现了 Vue 3 Renderer 的可插拔架构设计，Teleport 是一个“逻辑归属在组件树，渲染归属在外部容器”的抽象节点类型。

## 有哪些内置组件
<component>动态组件

作用：用于根据条件动态地渲染不同的组件。

<transition>过渡组件

作用：为元素或组件的插入、更新和移除添加过渡效果。

<teleport>传送门组件

作用：将一个组件的模板内容渲染到指定的 DOM 节点位置，而不是在组件自身的位置。

<keep-alive>缓存组件

作用：在组件切换时缓存不活动的组件实例，避免重复渲染，提高性能。

## 事件修饰符

.stop	调用 event.stopPropagation() 阻止事件冒泡。
.prevent	调用 event.preventDefault() 阻止默认事件行为。
.capture	使用事件捕获模式添加事件监听器，而不是冒泡模式。
.self	仅当事件是从事件绑定的元素本身触发时才触发回调。
.once	事件只触发一次，之后移除事件监听器。
.passive	以 { passive: true } 模式添加监听器，表示不会调用 preventDefault()。

.lazy 是一个输入绑定修饰符，用于 v-model 指令。它的主要作用是改变数据同步的时机：默认情况下，使用 v-model 绑定的输入字段会在每次 input 事件触发时同步数据（即用户输入时实时同步），而通过添加 .lazy 修饰符后，数据同步会改为在 change 事件发生时才进行

.exact确保只有在指定的系统修饰键（如 ctrl、alt、shift、meta）组合完全匹配时，事件处理函数才会被触发。

## reactive() 的局限性有哪些
1. 基本数据类型不是响应式的
reactive 只能将对象或数组转换为响应式对象。对于基本数据类型（如字符串、数字、布尔值等），reactive 无法将其转换为响应式的，应使用 ref。

2. 不能替换整个对象
  - 当你使用 reactive 创建响应式对象后，如果尝试直接替换掉整个响应式对象，新对象不会自动成为响应式的。
  - 这是因为 reactive 返回的是原始对象的代理（Proxy），直接替换原始对象并不会改变已经建立的代理关系。
  - 解决办法是通过修改对象的属性来实现，或者使用 ref 并对其 .value 进行替换，后者可以保持响应性。

3. 对解构操作不友好
Vue 3 reactive 对象在进行解构操作时会失去其响应性，这是因为解构操作本质上是对对象属性的值进行了一次拷贝。如果对一个响应式对象进行解构赋值，那么得到的新变量不会是响应式的。为了在解构后保持响应性，Vue 3 提供了 toRefs 和 toRef 函数。
  - toRefs 可以将 reactive 对象中的每个属性转换为一个 ref 对象，从而保留其响应性。
  - toRef 可以为 reactive 对象的某个属性创建一个 ref 引用，同样可以保持其响应性。

4. 返回 Proxy 对象
reactive 返回的是原始对象的 Proxy。这意味着，比较原始对象和通过 reactive 处理后的对象时，必须注意它们并不严格相等（===）。

## vue 中 reactive() 返回的为何是一个原始对象的 Proxy，好处
依赖、深层次、动态属性、性能、更多数据类型

1. 智能依赖跟踪
Proxy 允许 Vue 精确地控制对象属性的读取和修改。这意味着 Vue 可以智能地追踪哪些组件依赖于哪些数据，只有当相关数据发生改变时，依赖那些数据的组件才会重新渲染。这种细粒度的依赖跟踪可以极大地提高应用的性能。
2. 深层嵌套的响应式转换
通过使用 Proxy，Vue 能够在访问任意层级的属性时，将该属性转换为响应式的
3. 动态属性的响应式支持
与 Vue 2 的 Object.defineProperty 方法相比，Proxy 可以让新增的属性也成为响应式的
4. 更好的性能
由于 Proxy 提供了更精细的拦截能力，Vue 可以减少不必要的检查和依赖收集工作，从而提高了响应系统的整体性能。
5. 支持更多数据类型
通过 Proxy，Vue 3 的响应式系统不仅支持对象和数组，还能支持 Map、Set 等更多的 JavaScript 数据类型。

## 深层嵌套的对象、数组或者 JavaScript 内置的数据结构，比如 Map 等， 在响应式使用方面， ref 和 reactive 有何区别吗
当处理深层嵌套的对象、数组或内置数据结构时：

reactive 默认提供深度响应式，并且可以使 Map、Set 等内置数据结构变为响应式。
ref 在赋值对象或数组时自动将其转换为响应式，但不适用于 Map 或 Set 等内置数据结构的深度响应。

一般情况下，对于复杂或深层嵌套的数据结构，reactive 更加适合。对于基本数据类型或不太复杂的嵌套数据，ref 可以提供方便的响应式转换。


## watch 和 watchEffect 场景上有何区别
watch 更偏“精确监听”，只在指定数据变化时触发，并且可以拿到新旧值；
watchEffect 更偏“自动运行副作用”，会立即执行，并自动追踪回调中用到的所有响应式数据，适用于初始化逻辑和不确定依赖场景。

用来在回调函数重新执行之前进行清理。
可以接收第二个参数，用来控制侦听器的行为。
```
watchEffect((onInvalidate) => {
  const timer = setInterval(() => {
    /* ... */
  }, 1000);

  // 当侦听器重新执行或组件卸载时，清理定时器
  onInvalidate(() => {
    clearInterval(timer);
  });
});

watchEffect(
  () => {
    /* ... */
  },
  {
    flush: "post", // 'pre', 'post', 或 'sync'
  }
);
```

## 侦听器在什么情况下是需要清理副作用的
清理副作用是为了防止不必要的资源占用和潜在的内存泄漏，尤其是在使用外部资源、设置定时器、或订阅数据时。Vue 提供的onInvalidate回调允许在侦听器重新运行之前或组件销毁时执行清理逻辑，确保应用资源被适当管理和释放。
1. 使用定时器时
```
watchEffect((onInvalidate) => {
  const timer = setInterval(() => {
    // 执行一些逻辑
  }, 1000);

  // 清理函数
  onInvalidate(() => {
    clearInterval(timer);
  });
});
```
2. 订阅外部或异步资源时
```
watchEffect((onInvalidate) => {
  const ws = new WebSocket("ws://example.com/feed");

  ws.onmessage = (message) => {
    // 处理消息
  };

  // 侦听器清理函数
  onInvalidate(() => {
    ws.close();
  });
});
```
3. 响应式引用发生变化时
```
const user = ref(null);

watchEffect((onInvalidate) => {
  // 假设fetchUser返回一个取消订阅或清理资源的函数
  const unsubscribe = fetchUser(user.value, (newUser) => {
    user.value = newUser;
  });

  onInvalidate(() => {
    unsubscribe();
  });
});
```

## 介绍一下 Provide
Vue 3 中的 provide 和 inject 功能提供了一种方法，允许祖先组件将数据“提供”给它的所有后代组件，无论后代组件位于组件树的何处，而不必通过所有的组件层层传递属性（props）。这对于深层嵌套的组件或跨多个组件共享状态特别有用。
1. provide 和 inject 提供的依赖关系不是可靠的，并且不应该在业务逻辑中频繁使用，以避免复杂的跨组件通讯导致应用难以维护。它通常被用于开发可复用的插件或高阶组件。
2. 使用这两个选项时，注入的数据在后代组件中并不是响应式的，除非使用了 Vue 的响应式系统（如 reactive、ref）来提供这些数据。
3. 如果 inject 未找到提供的键，则它默认返回 undefined。你可以通过提供第二个参数作为默认值来改变这一行为。

## 子组件是否能使用 未定义的 props
默认情况下不能使用， 堵车使用 $props可以获取

## 子组件定义接受的 props 方式有哪些
1. 数组形式
2. 对象形式（具有类型检查）
3. 对象形式（具有类型检查和默认值）
4. 对象形式（具有验证函数）
```
props: {
  isPublished: Boolean,
  title: {
    type: String,
    required: true
  },
  age: {
    type: Number,
    validator: function (value) {
      // 这个值必须匹配下面的条件
      return value > 0 && value < 100;
    }
  }
}
```

## 动态插槽名
`<slot :name="dynamicSlotName"></slot>`
## 条件插槽
条件插槽可以通过结合使用 $slots 属性与 v-if 来实现动态地根据特定条件渲染不同的内容到插槽中。

## 如何自定义指令
一、全局自定义指令
Vue.directive()
bind,inserted, update, componentUpdated, unbind
二、局部自定义指令
```
export default {
  directives: {
    "my-directive": {
      bind(el, binding, vnode) {
        // 指令定义
      },
    },
  },
};
```
三、指令定义对象的参数说明
1. el：指令所绑定的元素，可以通过这个参数来操作元素的属性、样式等。
2. binding：一个对象，包含以下属性：
3. value：指令的绑定值，例如在v-my-directive="someValue"中，value就是someValue的值。
    - arg：指令的参数，如果指令是v-my-directive:argName，那么arg就是argName。
    - modifiers：一个对象，包含指令的修饰符。
    - vnode：虚拟节点，代表指令所绑定的元素的虚拟节点。
4. oldVnode：上一个虚拟节点，仅在update和componentUpdated钩子中可用。

四、自定义指令的应用场景

操作 DOM 元素：例如，在特定条件下为元素添加或移除类名、设置样式、监听元素的事件等。
实现复杂的交互效果：比如拖拽、缩放、滚动监听等。
数据格式化：在将数据绑定到元素之前对数据进行格式化处理。

## vue 全局注册组件很方便，为何不都是用全局注册
1. 应用启动性能
2. 代码分割和懒加载
局部注册组件可以结合 Vue 的动态组件和 Webpack 的代码分割特性，按需加载组件，减少首屏加载时间。
3. 命名空间问题
全局注册所有组件可能会导致组件命名冲突。
4. 项目可维护性
使用全局注册，开发者可能难以追踪某个组件的使用情况，因为它可以在任何地方被引用。
5. 树摇（Tree Shaking）
对于基于模块的工具链（如Webpack），局部注册的组件更容易被优化，因为未使用的组件可以在构建阶段被摇掉（Tree Shaking），减少最终的包大小。而全局注册的组件则更难被摇掉，因为构建工具很难确定它们是否在某处被使用。

