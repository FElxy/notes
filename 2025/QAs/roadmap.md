# JavaScript 核心 & 异步编程
1. 事件循环
  * 什么是事件循环
  * setTimeout，promise执行顺序 
  * 事件循环的使用案例
https://github.com/pro-collection/interview-question/issues/142
https://github.com/pro-collection/interview-question/issues/1023
2. 宏任务、微任务
  * 举例哪些是宏任务、微任务
  * 为什么要设计微任务机制？它解决了什么问题？
https://github.com/pro-collection/interview-question/issues/47
https://github.com/pro-collection/interview-question/issues/998
4. 浏览器的事件循环和node的事件循环区别
https://github.com/pro-collection/interview-question/issues/202
5. 变量
  * 变量、变量提升
  * 块级作用域
6. 调用栈、执行上下文
  * 什么是调用栈
  * 什么是执行上下文
  * 执行上下文包含了哪些内容
7. 作用域
  * 什么是作用域、作用域链，如何形成的
  * 什么是闭包
  * 如何产生的闭包
  * 闭包有什么应用场景和潜在问题
8. this
  * this的理解
6. 异步方案
  * Promise 有几种状态？状态改变是不可逆的吗？
  * Promise 中 throw new Error() 和 Promise.reject() 有什么区别？
  * 如何实现 Promise.all 和 Promise.race？如果其中一个 Promise 失败，它们的行为分别是什么？
  * async/await 的原理是什么？它和 Generator 有什么关系？
  * 使用 async/await 时，如何优雅地处理错误？

# 浏览器渲染原理 & 性能优化

## 从输入 URL 到页面展示，这中间发生了什么？
1. 用户输入
  * 如果是搜索内容，地址栏使用默认搜索引擎搜索内容
  * 如果是URL，当前页面执行beforeunload事件，如果没有监听或者同意继续后，进入加载状态
2. URL请求过程
  * 浏览器进程通过IPC把URL请求发送到网络进程，网络进程查找本地是否缓存了该资源，如果有缓存直接返回资源；如果没有进入网络请求流程。
  * DNS：进行DNS解析，获取IP地址。如果是HTTPS，则需要建立TLS连接。（建立过程）
  * TCP：利用IP地址和服务器建立TCP连接，浏览器构建请求信息。服务器接收到请求信息后，生成响应数据。网络进程接收到响应数据后，解析响应头。
    a. 重定向：解析到响应头如果状态码是301或者302，网络进程从location字段读取重定向地址，发起http请求
    b. 响应处理：如果是200，根据Content-Type，如果字节流类型的交给浏览器的下载管理器；如果是HTML，浏览器继续进行导航流程。

3. 准备渲染进程
  * 如果是同一站点，那么复用渲染进程；其他情况，打开新的页面使用新的渲染进程
  * 渲染进程准备好了后，进入到提交稳稳当阶段
4. 提交文档
  * 浏览器进程将网络进程接收到的HTML数据提交给渲染进程
    a. 浏览器进程接收到网络进程的响应头数据之后，想渲染进程发起“提交文档”消息
    b. 渲染进程接收到消息后，和网络进程建立传输数据管道
    c. 文档数据传输完成之后，渲染进程返回“确认提交”的消息给浏览器进程
    d. 浏览器进程收到消息后，更新浏览器界面状态（安全、地址栏URL、历史状态）
  导航阶段完成后，进入到渲染阶段
5. 渲染阶段
  * 渲染路径 构建 -> 布局 -> 绘制 -> 合成
  * 重排（Reflow）和重绘（Repaint）

6. 性能优化
  * 缩短首屏加载时间
  * 性能分析工具、指标
  * 前端缓存策略