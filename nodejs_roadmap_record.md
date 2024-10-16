
# command line app

# api
## frameword
1. Express.js
特点：轻量级、灵活，适用于构建 web 应用和 API。
使用场景：RESTful API、单页应用等。
2. Koa.js
特点：由 Express 的原始开发者创建，使用 async/await，提供更简洁的中间件。
使用场景：需要控制中间件执行顺序的应用。
3. NestJS
特点：基于 TypeScript 的进阶框架，采用模块化架构，支持依赖注入。
使用场景：企业级应用、微服务架构。
4. Hapi.js
特点：注重配置和插件化，提供丰富的功能，适合大规模应用。
使用场景：需要安全性和可扩展性的应用。
8. Next.js
特点：适用于构建服务器渲染的 React 应用，支持静态网站生成。
使用场景：需要 SEO 优化的 React 应用。
9. Nuxt.js
特点：适用于 Vue.js，支持服务器渲染和静态网站生成。
使用场景：需要 SEO 优化的 Vue 应用。

## make request
http
```js
const https = require('https');

const options = {
  host: 'jsonplaceholder.typicode.com',
  path: '/users/1',
  method: 'DELETE',
  headers: {
    'Accept': 'application/json',
  }
};

const request = https.request(options, (res) => {
  if (res.statusCode !== 200) {
    console.error(`Did not get an OK from the server. Code: ${res.statusCode}`);
    res.resume();
    return;
  }

  let data = '';

  res.on('data', (chunk) => {
    data += chunk;
  });

  res.on('close', () => {
    console.log('Deleted user');
    console.log(JSON.parse(data));
  });
});

request.end();

request.on('error', (err) => {
  console.error(`Encountered an error trying to make a request: ${err.message}`);
});
```

Axios
Axios is a promise-based HTTP Client for node.js and the browser.

Fetch
browser
The Fetch API provides a JavaScript interface for making HTTP requests and processing the responses.
Fetch is the modern replacement for XMLHttpRequest: unlike XMLHttpRequest, which uses callbacks, Fetch is promise-based and is integrated with features of the modern web such as service workers and Cross-Origin Resource Sharing (CORS).

node
Added in: v17.5.0, v16.15.0
v18.0.0	
No longer behind --experimental-fetch CLI flag.
v21.0.0	
A browser-compatible implementation of the fetch() function.
No longer experimental.

## validate
jwt
JWT, or JSON Web Token, is an open standard used to share security information between two parties — a client and a server. Each JWT contains encoded JSON objects, including a set of claims. JWTs are signed using a cryptographic algorithm to ensure that the claims cannot be altered after the token is issued.

Passport js
signin use 
Username & Password
Sign In with Google
Sign In with Facebook
Email Magic Link
Auth0 Integration
> https://www.passportjs.org/

# async programming
> https://javascript.info/settimeout-setinterval
promises
The Promise object represents the eventual completion (or failure) of an asynchronous operation and its resulting value.
A Promise is in one of these states:

pending: initial state, neither fulfilled nor rejected.
fulfilled: meaning that the operation was completed successfully.
rejected: meaning that the operation failed.
> https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise

Async/Await
> https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function

Callbacks

There are two ways of running something regularly.
One is setInterval. The other one is a nested setTimeout
```js
let i = 1;
setInterval(function() {
  func(i++);
}, 100);

let i = 1;
setTimeout(function run() {
  func(i++);
  setTimeout(run, 100);
}, 100);
```
For setInterval the internal scheduler will run func(i++) every 100ms:
The real delay between func calls for setInterval is less than in the code!
The nested setTimeout guarantees the fixed delay (here 100ms).
setTimeout可以保证执行完函数后进行延迟，setInterval是每隔100ms就执行，函数执行时间包含在100ms内

Zero delay setTimeout
There’s a special use case: setTimeout(func, 0), or just setTimeout(func).

Summary
Methods setTimeout(func, delay, ...args) and setInterval(func, delay, ...args) allow us to run the func once/regularly after delay milliseconds.
To cancel the execution, we should call clearTimeout/clearInterval with the value returned by setTimeout/setInterval.
Nested setTimeout calls are a more flexible alternative to setInterval, allowing us to set the time between executions more precisely.
Zero delay scheduling with setTimeout(func, 0) (the same as setTimeout(func)) is used to schedule the call “as soon as possible, but after the current script is complete”.
The browser limits the minimal delay for five or more nested calls of setTimeout or for setInterval (after 5th call) to 4ms. That’s for historical reasons.
Please note that all scheduling methods do not guarantee the exact delay.

For example, the in-browser timer may slow down for a lot of reasons:

**The CPU is overloaded.**
**The browser tab is in the background mode.**
**The laptop is on battery saving mode.**
**All that may increase the minimal timer resolution (the minimal delay) to 300ms or even 1000ms depending on the browser and OS-level performance settings.**

Any setTimeout will run only after the current code has finished.
The i will be the last one: 100000000.
```js
let i = 0;

setTimeout(() => alert(i), 100); // ?

// assume that the time to execute this function is >100ms
for(let j = 0; j < 100000000; j++) {
  i++;
}
```
# database
ORM
An ORM is known as Object Relational Mapper. This is a tool or a level of abstraction which maps(converts) data in a relational database into programmatic objects that can be manipulated by a programmer using a programming language(usually an OOP language).

Sequelize
TypeORM
Prisma
# threads


# other

## Working with Files
process.cwd()
The process.cwd() method returns the **current working directory** of the Node.js process.
i.e. the directory from which you invoked the node command.

__dirname returns the directory name of the directory containing the JavaScript source code file


## Nodejs与浏览器的区别

ecosystem
| nodejs | browser |
| ------ | ------- |
|modules like filesystem|DOM, or other Web Platform APIs like Cookies|
|-|support old version, need use babel|
|supports both the CommonJS and ES module systems,In practice, this means that you can use both require() and import in Node.js, while you are  |limited to import in the browser.|



## Nodejs退出的5种方式
1. 等待程序执行完成，自动退出。如果有类似setInterval，使用Ctrl + C退出
2. 使用process.exit()，process.exit(code)可以传递Exit code
3. process.kill()，process.kill(pid[, signal]) 
```js
console.log('Code running'); 
process.on('exit', function(code) { 
 return console.log(`exiting the code ${code}`); 
}); 
setTimeout((function() { 
return process.kill(process.pid); 
}), 5000); 
```
4. process.on()
```js
process.on('exit', function(code) { 
return console.log(`exiting the code implicitly ${code}`); 
}); 
```
5. process.abort
it will immediately terminate the node.js program and then create a core file.
https://www.knowledgehut.com/blog/web-development/node-js-process-exit

## Keep App Running
using nodemon to restart the process automatically.
Since Node.js 18.11.0, you can run Node with the --watch flag to reload
```
node -w app.js
node -w app.js lib/ utils/
node --watch app.js
```

## template engine
ejs 
```html
<header>
  <%- include('../partials/header'); %>
</header>

<header>
  <%- include('../partials/header', {variant: 'compact'}); %>
</header>

<em>Variant: <%= typeof variant != 'undefined' ? variant : 'default' %></em>
```
pug
```js
//- template.pug
p #{name}'s Pug source code!
const pug = require('pug');

// Compile the source code
const compiledFunction = pug.compileFile('template.pug');

// Render a set of data
console.log(compiledFunction({
  name: 'Timothy'
}));
// "<p>Timothy's Pug source code!</p>"

// Render another set of data
console.log(compiledFunction({
  name: 'Forbes'
}));
// "<p>Forbes's Pug source code!</p>"
```

marko
```js
class {
    onCreate() {
        this.state = {
            count: 0
        };
    }
    increment() {
        this.state.count++;
    }
}

<div>The current count is ${state.count}</div>
<button on-click("increment")>Click me!</button>
```

# Error Handling
Operational Errors
- failed to connect to server
- failed to resolve hostname
- invalid user input
- request timeout
- server returned a 500 response
- socket hang-up
- system is out of memory

Programmer Errors
- called an asynchronous function without a callback
- did not resolve a promise
- did not catch a rejected promise
- passed a string where an object was expected
- passed an object where a string was expected
- passed incorrect parameters in a function

System Errors
1. EACCES - Permission denied
2. EADDRINUSE - Address already in use
3. ECONNRESET - Connection reset by peer
4. EEXIST - File exists
5. EISDIR - Is a directory
6. EMFILE - Too many open files in system
7. ENOENT - No such file or directory
8. ENOTDIR - Not a directory
9. ENOTEMPTY - Directory not empty
10. ENOTFOUND - DNS lookup failed
11. EPERM - Operation not permitted
12. EPIPE - Broken Pipe
13. ETIMEDOUT - Operation timed out

# log
Winston
> https://github.com/winstonjs/winston
morgan

# Version Format
Semantic Versioning
A semantic version number consists of three parts separated by dots:

MAJOR: Incremented when there are incompatible API changes.
MINOR: Incremented when new functionality is added in a backwards-compatible manner.
PATCH: Incremented when bug fixes are made without affecting the API.
Example: 1.2.3
1 is the major version.
2 is the minor version.
3 is the patch version.

