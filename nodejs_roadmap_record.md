
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

A process.nextTick callback is added to process.nextTick queue. A Promise.then() callback is added to promises microtask queue. A setTimeout, setImmediate callback is added to macrotask queue.
Calling setTimeout(() => {}, 0) will execute the function at the end of next tick, much later than when using nextTick() which prioritizes the call and executes it just before the beginning of the next tick.
```js
const baz = () => console.log('baz');
const foo = () => console.log('foo');
const zoo = () => console.log('zoo');
const start = () => {
  console.log('start');
  setImmediate(baz);
  new Promise((resolve, reject) => {
    resolve('bar');
  }).then(resolve => {
    console.log(resolve);
    process.nextTick(zoo);
  });
  process.nextTick(foo);
};
start();
// start foo bar zoo baz
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
__filename
Create and edit controller.js in the api subdirectory in the src directory:
```js
console.log(__dirname)      // "/Users/Sam/dirname-example/src/api"
console.log(process.cwd())  // "/Users/Sam/dirname-example"

const fs = require('fs');
const path = require('path');
const dirPath = path.join(__dirname, '/pictures');

fs.mkdirSync(dirPath);

console.log(__filename);
// Prints: /Users/mjr/example.js
console.log(__dirname);
// Prints: /Users/mjr
```


## Work with Files using the fs Module in Node.js
Step 1 — Reading Files with readFile()
```js
const fs = require('fs').promises;

async function readFile(filePath) {
  try {
    const data = await fs.readFile(filePath);
    console.log(data.toString());
  } catch (error) {
    console.error(`Got an error trying to read the file: ${error.message}`);
  }
}
```

Step 2 — Writing Files with writeFile()
```js
const fs = require('fs').promises;

async function openFile() {
  try {
    const csvHeaders = 'name,quantity,price'
    await fs.writeFile('groceries.csv', csvHeaders);
  } catch (error) {
    console.error(`Got an error trying to write to a file: ${error.message}`);
  }
}

async function addGroceryItem(name, quantity, price) {
  try {
    const csvLine = `\n${name},${quantity},${price}`
    await fs.writeFile('groceries.csv', csvLine, { flag: 'a' });
  } catch (error) {
    console.error(`Got an error trying to write to a file: ${error.message}`);
  }
}

(async function () {
  await openFile();
  await addGroceryItem('eggs', 12, 1.50);
  await addGroceryItem('nutella', 1, 4);
})();
```
```js
var fs = require('fs');

fs.appendFile('mynewfile1.txt', ' This is my text.', function (err) {
  if (err) throw err;
  console.log('Updated!');
});
```
This object has a flag key with the value a. Flags tell Node.js how to interact with the file on the system. By using the flag a, you are telling Node.js to append to the file, not overwrite it. If you don’t specify a flag, it defaults to w, which creates a new file if none exists or overwrites a file if it already exists. 

Step 3 — Deleting Files with unlink()
```js
const fs = require('fs').promises;

async function deleteFile(filePath) {
  try {
    await fs.unlink(filePath);
    console.log(`Deleted ${filePath}`);
  } catch (error) {
    console.error(`Got an error trying to delete the file: ${error.message}`);
  }
}

deleteFile('groceries.csv');
```
Step 4 — Moving Files with rename()
```js
const fs = require('fs').promises;

async function moveFile(source, destination) {
  try {
    await fs.rename(source, destination);
    console.log(`Moved file from ${source} to ${destination}`);
  } catch (error) {
    console.error(`Got an error trying to move the file: ${error.message}`);
  }
}

moveFile('greetings-2.txt', 'test-data/salutations.txt');
```

## Node.js File Paths
```js
import path from 'node:path';

const notes = '/users/joe/notes.txt';

path.dirname(notes); // /users/joe
path.basename(notes); // notes.txt
path.extname(notes); // .txt

path.basename(notes, path.extname(notes)); // notes
```
```js
const name = 'joe';
path.join('/', 'users', name, 'notes.txt'); // '/users/joe/notes.txt'
path.resolve('joe.txt'); // '/Users/joe/joe.txt' if run from my home folder
path.resolve('tmp', 'joe.txt'); // '/Users/joe/tmp/joe.txt' if run from my home folder

```
If the first parameter starts with a slash, that means it's an absolute path:
```js
path.resolve('/etc', 'joe.txt'); // '/etc/joe.txt'
```
path.normalize() is another useful function, that will try and calculate the actual path, when it contains relative specifiers like . or .., or double slashes:
```js
path.normalize('/users/joe/..//test.txt'); // '/users/test.txt'
```
> https://nodejs.org/api/path.html


## thread
适合做的：responding to an Http request, talking to a database, talking to other servers 
不适合的：CPU bound operations like calculating the Fibonacci of a number or checking if a number is prime or not or heavy machine learning stuff

### child processes
#### child_process.spawn()
spawn("comand to run","array of arguments",optionsObject)
```js
const { spawn } = require("child_process")
app.get("/ls", (req, res) => {
  const ls = spawn("ls", ["-lash", req.query.directory])
  ls.stdout.on("data", data => {
    //Pipe (connection) between stdin,stdout,stderr are established between the parent
    //node.js process and spawned subprocess and we can listen the data event on the stdout

    res.write(data.toString()) //date would be coming as streams(chunks of data)
    // since res is a writable stream,we are writing to it
  })
  ls.on("close", code => {
    console.log(`child process exited with code ${code}`)
    res.end() //finally all the written streams are send back when the subprocess exit
  })
})
```
#### child_process.fork()
child_process.fork() is specifically used to spawn new nodejs processes. Like spawn, the returned childProcess object will have built-in IPC communication channel that allows messages to be passed back and forth between the parent and child.
fork("path to module","array of arguments","optionsObject")
```js
const { fork } = require("child_process")
const childProcess = fork("./forkedchild.js") //the first argument to fork() is the name of the js file to be run by the child process
childProcess.send({ number: parseInt(req.query.number) }) //send method is used to send message to child process through IPC

// forkedchild.js
process.on("message", message => {
Copy
  //child process is listening for messages by the parent process
  const result = isPrime(message.number)
  process.send(result)
  process.exit() // make sure to use exit() to prevent orphaned processes
})

```

#### Worker threads
```js
const { workerData, parentPort } = require("worker_threads")
//workerData will be the second argument of the Worker constructor in multiThreadServer.js
const start = workerData.start
const end = workerData.end
// ....
parentPort.postMessage({
  //send message with the result back to the parent process
  start: start,
  end: end,
  result: sum,
})

// main
const { Worker } = require("worker_threads")

function runWorker(workerData) {
  return new Promise((resolve, reject) => {
    //first argument is filename of the worker
    const worker = new Worker("./sumOfPrimesWorker.js", {
      workerData,
    })
    worker.on("message", resolve) //This promise is gonna resolve when messages comes back from the worker thread
    worker.on("error", reject)
    worker.on("exit", code => {
      if (code !== 0) {
        reject(new Error(`Worker stopped with exit code ${code}`))
      }
    })
  })
}

  const worker1 = runWorker({ start: start1, end: end1 })
  const worker2 = runWorker({ start: start2, end: end2 })
  const worker3 = runWorker({ start: start3, end: end3 })
  const worker4 = runWorker({ start: start4, end: end4 })
  //Promise.all resolve only when all the promises inside the array has resolved
  Promise.all([worker1, worker2, worker3, worker4])

```
This is because, we are dividing the work into 4 equal parts and allocating each part to a worker and parallelly (at the same time) executing the task.



#### cluster
Cluster is mainly used for vertically (adding more power to your existing machine) scale your nodejs web server. It is built on top of the child_process module. In an Http server, the cluster module uses child_process.fork() to automatically fork processes and sets up a master-slave architecture where the parent process distributes the incoming request to the child processes in a round-robin fashion. Ideally, the number of processes forked should be equal to the number of cpu cores your machine has.
const cluster = require("cluster")
```js
if (cluster.isMaster) {
  masterProcess()
} else {
  childProcess()
}
```
Even though Node js provides great support for multi-threading, that doesn't necessarily mean we should always make our web applications multi-threaded. Node js is built in such a way that the default single-threaded behavior is preferred over the multi-threaded behaviour for web-servers because web-servers tend to be IO-bound and nodejs is great for handling asynchronous IO operations with minimal system resources and Nodejs is famous for this feature. The extra overhead and complexity of another thread or process makes it really difficult for a programmer to work with simple IO tasks. But there are some cases where a web server does CPU bound operations and in such cases, it is really easy to spin up a worker thread or child process and delegate that task. So, our design architecture really boils down to our application's need and requirements and we should make decisions based on that.

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

# streams
1. Memory efficiency: you don’t need to load large amounts of data in memory before you are able to process it
2. Time efficiency: it takes significantly less time to start processing data as soon as you have it, rather than having to wait with processing until the entire payload has been transmitted

4 types
Writable: `fs.createWriteStream()`
Readable: `fs.createReadStream()`
Duplex: streams that are both Readable and Writable.  `net.Socket`
Transform: streams that can modify or transform the data as it is written and read.  For example, in the instance of file-compression, you can write compressed data and read decompressed data to and from a file.

in a Node.js based HTTP server, request is a readable stream and response is a writable stream. TCP sockets, TLS stack and other connections are all based on Node.js streams.
```js
const Stream = require('stream')
const readableStream = new Stream.Readable()
readableStream.push('ping!')
readableStream.push('pong!')
```
async iterator
```js
import * as fs from 'fs';

async function logChunks(readable) {
  for await (const chunk of readable) {
    console.log(chunk);
  }
}

const readable = fs.createReadStream(
  'tmp/test.txt', {encoding: 'utf8'});
logChunks(readable);
```

```js
import {Readable} from 'stream';

async function readableToString2(readable) {
  let result = '';
  for await (const chunk of readable) {
    result += chunk;
  }
  return result;
}

const readable = Readable.from('Good morning!', {encoding: 'utf8'});
assert.equal(await readableToString2(readable), 'Good morning!');
```

`stream.Readable.from(iterable, [options])`
```js
const { Readable } = require('stream');

async function * generate() {
  yield 'hello';
  yield 'streams';
}

const readable = Readable.from(generate());

readable.on('data', (chunk) => {
  console.log(chunk);
});
```

Two Reading Modes
flowing mode,data is read from the underlying system automatically
paused mode,the stream.read() method must be called explicitly to read chunks of data from the stream.

```js
var fs = require("fs");
var data = '';

var readerStream = fs.createReadStream('file.txt'); //Create a readable stream

readerStream.setEncoding('UTF8'); // Set the encoding to be utf8. 

// Handle stream events --> data, end, and error
readerStream.on('data', function(chunk) {
   data += chunk;
});

readerStream.on('end',function() {
   console.log(data);
});

readerStream.on('error', function(err) {
   console.log(err.stack);
});
```

```js
var fs = require('fs');
var readableStream = fs.createReadStream('file.txt');
var data = '';
var chunk;

readableStream.on('readable', function() {
    while ((chunk=readableStream.read()) != null) {
        data += chunk;
    }
});

readableStream.on('end', function() {
    console.log(data)
});
```
All Readable streams begin in paused mode but can be switched to flowing mode in one of the following ways:
Adding a 'data' event handler.
Calling the stream.resume() method.
Calling the stream.pipe() method to send the data to a Writable.

The Readable can switch back to paused mode using one of the following:

If there are no pipe destinations, by calling the stream.pause() method.
If there are pipe destinations, by removing all pipe destinations. Multiple pipe destinations may be removed by calling the stream.unpipe() method.

```js
// Write 'hello, ' and then end with 'world!'.
const fs = require('fs');
const file = fs.createWriteStream('example.txt');
file.write('hello, ');
file.end('world!');
// Writing more now is not allowed!
```
Calling the writable.end() method signals that no more data will be written to the Writable. If provided, the optional callback function is attached as a listener for the 'finish' event.

Using a writable stream you can read data from a readable stream:
```js
const Stream = require('stream')

const readableStream = new Stream.Readable()
const writableStream = new Stream.Writable()

writableStream._write = (chunk, encoding, next) => {
    console.log(chunk.toString())
    next()
}

readableStream.pipe(writableStream)

readableStream.push('ping!')
readableStream.push('pong!')

writableStream.end()
```
You can also use async iterators to write to a writable stream, which is recommended
```js
import * as util from 'util';
import * as stream from 'stream';
import * as fs from 'fs';
import {once} from 'events';

const finished = util.promisify(stream.finished); // (A)

async function writeIterableToFile(iterable, filePath) {
  const writable = fs.createWriteStream(filePath, {encoding: 'utf8'});
  for await (const chunk of iterable) {
    if (!writable.write(chunk)) { // (B)
      // Handle backpressure
      await once(writable, 'drain');
    }
  }
  writable.end(); // (C)
  // Wait until done. Throws if there are errors.
  await finished(writable);
}

await writeIterableToFile(
  ['One', ' line of text.\n'], 'tmp/log.txt');
assert.equal(
  fs.readFileSync('tmp/log.txt', {encoding: 'utf8'}),
  'One line of text.\n');
```

pipeline()
Piping is a mechanism where we provide the output of one stream as the input to another stream. It is normally used to get data from one stream and to pass the output of that stream to another stream. 
piping is used to process streamed data in multiple steps.

```js
const { pipeline } = require('stream');
const fs = require('fs');
const zlib = require('zlib');

// Use the pipeline API to easily pipe a series of streams
// together and get notified when the pipeline is fully done.
// A pipeline to gzip a potentially huge video file efficiently:

pipeline(
  fs.createReadStream('The.Matrix.1080p.mkv'),
  zlib.createGzip(),
  fs.createWriteStream('The.Matrix.1080p.mkv.gz'),
  (err) => {
    if (err) {
      console.error('Pipeline failed', err);
    } else {
      console.log('Pipeline succeeded');
    }
  }
);
```

在 Node.js 中，`child_process`、`cluster` 和 `worker_threads` 是处理并发任务的三种不同方式。它们各有特点和适用场景。以下是它们的区别：

### 1. `child_process`
- **定义**：用于创建独立的子进程，并执行独立的任务。
- **工作方式**：每个子进程运行在其独立的内存空间中，与主进程通过消息传递（IPC，进程间通信）进行通信。
- **用途**：适用于执行不依赖主进程状态的任务，比如在子进程中运行一个外部命令、处理繁重的计算任务、或者执行文件 I/O 操作。
- **缺点**：由于每个进程有独立的内存空间，开销较大（内存占用更高），并且通信成本较高。
- **适用场景**：子进程适用于 CPU 密集型任务，比如图像处理、文件处理、执行外部程序等。

### 2. `cluster`
- **定义**：`cluster` 模块是基于 `child_process` 构建的，用于实现多核负载均衡。
- **工作方式**：`cluster` 模块允许创建多个 Node.js 进程（称为“工作进程”），它们共享同一个端口（例如 HTTP 服务器端口），由 `cluster` 模块自动分配请求给各个工作进程。
- **用途**：适用于在多核服务器上扩展 Node.js 应用程序，通过多进程实现负载均衡。
- **缺点**：与 `child_process` 一样，`cluster` 每个进程都有独立的内存空间，通信成本较高。并且不适用于共享大量状态信息的场景。
- **适用场景**：`cluster` 非常适合创建高并发的 HTTP 服务器或其他需要充分利用 CPU 核心的应用程序。

### 3. `worker_threads`
- **定义**：`worker_threads` 模块用于创建轻量级线程，线程之间共享同一个内存空间。
- **工作方式**：每个 Worker 线程可以并行执行任务，线程间可以共享内存（使用 `SharedArrayBuffer`），这使得在处理数据时无需拷贝大量数据，提高了性能。
- **用途**：适用于执行 CPU 密集型任务，例如大型数组或复杂计算的处理。
- **优点**：`worker_threads` 的开销低，线程间通信成本低，并且可以共享内存，这使它适合需要频繁通信的场景。
- **缺点**：由于线程共享内存，如果没有同步机制，容易引发数据竞争问题。
- **适用场景**：适合于 Node.js 内部进行密集型的计算或数据处理任务，尤其适合需要高效处理共享数据的场景，比如机器学习、图像处理等计算密集型任务。

### 总结对比

| 模块           | 进程/线程   | 是否共享内存 | 适用场景                      | 优缺点                        |
|----------------|-------------|--------------|-------------------------------|-------------------------------|
| `child_process`| 独立进程    | 否           | 独立任务，外部命令执行        | 开销大，进程间通信较复杂      |
| `cluster`      | 独立进程    | 否           | 多核负载均衡，高并发服务器     | 进程间通信复杂，开销较大      |
| `worker_threads`| 线程       | 是           | CPU 密集型任务，共享内存计算   | 开销小，但需处理数据竞争问题  |

选择哪种方式取决于具体需求：`worker_threads` 更适合数据密集型计算，而 `child_process` 和 `cluster` 适用于处理隔离任务或扩展服务器负载的情况。