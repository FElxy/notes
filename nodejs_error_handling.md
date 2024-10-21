
# Using Debugger
Add a debugger statement inside the code that you want to debug.
Run the node inspect index.js or node inspect server.js command to start the application in debug mode.
Access the URL chrome://inspect in your Chrome browser.
Click on the inspect link under the Remote Target section.
Click on the blue triangle icon to skip debugging if you don't want to start debugging your application from the first line of the index.js or server.js file.
Make an API call or do something that will trigger the code where you added the debugger; statement. This way you can debug the code line by line and find out the issue.



# Multiple ways to log the printstack trace in Nodejs
1. using try and catch
```js
try {
  throw new Error("Custom Error");
} catch (error) {
  console.log(error);
}
```
Error stack property
```js
console.log(new Error("custom error").stack);
```
Console trace function
```js
console.trace(new Error("My Error"));
```

2. using process unhandledRejection event
```js
process.on('unhandledRejection', function(err, promise) {
    console.error('Unhandled rejection',error);
});
```

3. use captureStackTrace function in error
```js
const myemp = { id: 11, name: "Eric" };
Error.captureStackTrace(myemp);
console.log(myemp.stack);
```

# How to deliver errors
1. Exceptions
throw exception, and try catch
If the error is allowed to bubble up the stack without being caught, it becomes an uncaughtException,
```js
function square(num) {
  if (typeof num !== 'number') {
    throw new TypeError(`Expected number but got: ${typeof num}`);
  }

  return num * num;
}

try {
  square('8');
} catch (err) {
  console.log(err.message); // Expected number but got: string
}
```
2. Error-first callbacks
```js
function (err, result) {}
```
If you want to use this error-first callback pattern in your own async functions, all you need to do is accept a function as the last argument and call it in the manner shown below:
```js
function square(num, callback) {
  if (typeof callback !== 'function') {
    throw new TypeError(`Callback must be a function. Got: ${typeof callback}`);
  }

  // simulate async operation
  setTimeout(() => {
    if (typeof num !== 'number') {
      // if an error occurs, it is passed as the first argument to the callback
      callback(new TypeError(`Expected number but got: ${typeof num}`));
      return;
    }

    const result = num * num;
    // callback is invoked after the operation completes with the result
    callback(null, result);
  }, 100);
}
```

make sure not to throw an exception from within the function because it won't be caught, even if you surround the code in a try/catch block. An asynchronous exception is not catchable because the surrounding try/catch block exits before the callback is executed. 
```js
try {
  square('8', (err, result) => {
    if (err) {
      throw err; // not recommended
    }

    console.log(result);
  });
} catch (err) {
  // This won't work
  console.error("Caught error: ", err);
}
```

3. Promise rejections

Any Node.js API that utilizes error-first callbacks for asynchronous error handling can be converted to promises using the built-in util.promisify() method. 
```js
const fs = require('fs');
const util = require('util');

const readFile = util.promisify(fs.readFile);
```
4. Event emitters
```js
const { EventEmitter } = require('events');

function emitCount() {
  const emitter = new EventEmitter();

  let count = 0;
  // Async operation
  const interval = setInterval(() => {
    count++;
    if (count % 4 == 0) {
      emitter.emit(
        'error',
        new Error(`Something went wrong on count: ${count}`)
      );
      return;
    }
    emitter.emit('success', count);

    if (count === 10) {
      clearInterval(interval);
      emitter.emit('end');
    }
  }, 1000);

  return emitter;
}
const counter = emitCount();

counter.on('success', (count) => {
  console.log(`Count is: ${count}`);
});

counter.on('error', (err) => {
  console.error(err.message);
});

counter.on('end', () => {
  console.info('Counter has ended');
});
```

Extending the error object
```js
class ApplicationError extends Error {
  constructor(message) {
    super(message);
    // name is set to the name of the class
    this.name = this.constructor.name;
  }
}

class ValidationError extends ApplicationError {
  constructor(message, cause) {
    super(message);
    this.cause = cause
  }
}

function validateInput(input) {
  if (!input) {
    throw new ValidationError('Only truthy inputs allowed', input);
  }

  return input;
}

try {
  validateInput(userJson);
} catch (err) {
  if (err instanceof ValidationError) {
    console.error(`Validation error: ${err.message}, caused by: ${err.cause}`);
    return;
  }

  console.error(`Other error: ${err.message}`);
}
```

# Types of errors
1. Operational errors(API request fails,A database connection is lost,open a file or write to it,user sends invalid input )
2. Programmer errors(Syntax errors, Type errors, Bad parameters, Reference errors, Failing to handle an operational error. )

Operational error handling
1. Report the error up the stack
and report the error up the stack so that it can be handled appropriately. 
2. Retry the operation
Network requests to external services may sometimes fail
3. Send the error to the client
When dealing with external input from users, it should be assumed that the input is bad by default. Therefore, the first thing to do before starting any processes is to validate the input and report any mistakes to the user promptly so that it can be corrected and resent. 
4. Abort the program

Preventing programmer errors
1. Adopt TypeScript
2. Define the behavior for bad parameters
3. Automated testing

# Uncaught exceptions and unhandled promise rejections
The appropriate use of the uncaughtException handler is to clean up any allocated resources, close connections, and log the error for later assessment before exiting the process.
```js
// better
process.on('uncaughtException', (err) => {
  Honeybadger.notify(error); // log the error in a permanent storage
  // attempt a gracefully shutdown
  server.close(() => {
    process.exit(1); // then exit
  });

  // If a graceful shutdown is not achieved after 1 second,
  // shut down the process completely
  setTimeout(() => {
    process.abort(); // exit immediately and generate a core dump file
  }, 1000).unref()
});
```