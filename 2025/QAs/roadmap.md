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

2. URL请求过程
  * DNS
  * TCP
  * HTTPS

3. 准备渲染进程

4. 提交文档

5. 渲染阶段