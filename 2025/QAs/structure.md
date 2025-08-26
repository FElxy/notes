

# 前端面试知识点分类整理

## 一、基础核心

### 1. HTML

* 语义化标签（header、main、section、article、aside、footer）
* meta 标签作用（charset、viewport、http-equiv、SEO相关）
* script 标签属性（defer、async 的区别）
* HTML5 新特性（音视频、canvas、localStorage/sessionStorage、drag\&drop）
* 可访问性（ARIA）

### 2. CSS

* 选择器优先级 & 继承
* 盒模型（content-box / border-box）
* Flexbox & Grid 布局
* 定位（relative、absolute、fixed、sticky）
* 响应式布局（媒体查询、vw/vh、rem/em、百分比）
* 动画 & 过渡（transition、animation、transform）
* BFC / IFC / stacking context
* CSS 性能优化（合并重绘回流、GPU 加速）
* CSS 预处理器 & CSS in JS

### 3. JavaScript 基础

* 数据类型 & 类型转换
* 原型 & 原型链
* this、作用域、闭包
* 执行上下文 & 变量提升
* 事件机制（冒泡、捕获、事件委托）
* ES6+（let/const、解构、模板字符串、箭头函数、class、Promise、async/await、Proxy、Symbol、BigInt）
* 模块化（ES Module vs CommonJS）
* 深拷贝 & 浅拷贝
* 防抖 & 节流
* 异步机制（事件循环、宏任务 & 微任务、消息队列）

---

## 二、浏览器原理

* 浏览器渲染流程（解析 HTML -> 构建 DOM -> 构建 CSSOM -> 渲染树 -> 布局 -> 绘制 -> 合成）
* 重绘 & 回流
* 浏览器缓存（强缓存、协商缓存、Service Worker）
* 跨域 & 解决方案（CORS、JSONP、proxy、postMessage、Nginx）
* 存储（cookie、localStorage、sessionStorage、IndexedDB）
* Web 安全（XSS、CSRF、Clickjacking、防御方法）
* Event Loop（浏览器 vs Node.js）

---

## 三、计算机网络

* HTTP 协议（请求方法、状态码）
* HTTP1.1 / HTTP2 / HTTP3 区别
* TCP 三次握手 & 四次挥手
* TCP vs UDP
* HTTPS & TLS 握手
* DNS 解析流程
* CDN 原理
* RESTful API / GraphQL

---

## 四、框架相关

### 1. React

* 生命周期（class & hooks）
* Hooks（useState、useEffect、useMemo、useCallback、useRef、useContext）
* 虚拟 DOM & Diff 算法
* 状态管理（Redux、MobX、Zustand、Recoil）
* Fiber 架构
* React 性能优化（memo、lazy、Suspense、代码分割）
* SSR（Next.js）

### 2. Vue

* Vue2 vs Vue3 区别
* 响应式原理（Object.defineProperty / Proxy）
* 组件通信（props、\$emit、provide/inject、Vuex、pinia）
* 生命周期
* 虚拟 DOM & Diff 算法
* Vue Router 原理
* Vue 性能优化

### 3. 其他框架

* Angular（模块、依赖注入、Zone.js）
* Svelte / SolidJS（编译时框架）

---

## 五、工程化

* 构建工具（Webpack、Vite、Rollup、esbuild）
* 模块打包原理
* Tree Shaking、Code Splitting
* Source Map
* Babel 原理
* CI/CD 流程
* 前端监控（性能监控、错误监控、埋点）
* 单元测试（Jest、Vitest、Cypress、Playwright）
* Lint & Prettier

---

## 六、性能优化

* 首屏优化（SSR、懒加载、骨架屏）
* 资源优化（压缩、合并、缓存、CDN）
* 图片优化（WebP、懒加载、响应式图片）
* 渲染优化（减少重绘回流、虚拟列表）
* 大量数据渲染优化（分页、虚拟滚动）
* 前端内存泄漏排查

---

## 七、Node.js & 全栈

* Node.js 事件循环
* 常用框架（Express、Koa、Nest.js）
* 中间件机制
* 文件系统 & 流
* cluster / worker\_threads
* JWT 鉴权
* GraphQL API

---

## 八、综合能力

* 数据结构与算法（排序、二分、链表、栈队列、树、图、动态规划）
* 设计模式（单例、工厂、观察者、发布订阅、装饰器）
* 大型前端架构（微前端、模块联邦）
* 常见手写题（深拷贝、防抖节流、new 实现、bind/call/apply 实现、Promise 实现、LRU 缓存）

---



1. **基础三件套（HTML/CSS/JS）** → 高频必问
2. **浏览器原理 & 网络** → 深度考察
3. **框架（React/Vue）** → 项目经验结合
4. **工程化 & 优化** → 中高级必备
5. **算法 & 设计模式** → 加分项

---
