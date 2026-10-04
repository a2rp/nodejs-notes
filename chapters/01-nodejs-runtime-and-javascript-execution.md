# 1. Node.js runtime and JavaScript execution

[Back to notes index](../README.md)

| [Previous: Notes index](../README.md) | [Notes index](../README.md) | [Next: Install Node.js and use the command line](./02-install-nodejs-and-command-line.md) |
| --- | --- | --- |

## What Node.js adds to JavaScript

JavaScript is the language. Node.js is a runtime that lets JavaScript run outside a web browser. It provides the V8 JavaScript engine together with APIs for tasks such as reading files, opening network connections, working with paths, and creating servers.

A browser page and a Node.js process share the JavaScript language, but their host APIs differ. A browser provides objects such as **window** and **document**. Node.js provides objects such as **process** and built-in modules such as **node:fs** and **node:http**.

## A first Node.js program

Create **hello.js**:

~~~js
console.log('Hello from Node.js')
console.log('Node version:', process.version)
console.log('Current folder:', process.cwd())
~~~

Run it from the folder containing the file:

~~~sh
node hello.js
~~~

**process.version** reports the runtime version. **process.cwd()** reports the current working directory, which is the directory from which the command was started. It is not necessarily the directory containing the JavaScript file.

## The JavaScript engine and host APIs

V8 parses and executes JavaScript. Node.js connects that engine to native runtime facilities. For example, **node:fs** exposes file operations and **node:http** exposes HTTP server and client APIs.

Use the **node:** prefix for built-in modules so the import clearly refers to Node.js:

~~~js
const path = require('node:path')
const os = require('node:os')

console.log('Home folder:', os.homedir())
console.log('Joined path:', path.join('notes', 'chapter.md'))
~~~

Save this as **built-ins.cjs** and run **node built-ins.cjs**. The **.cjs** extension explicitly selects CommonJS, which is covered in a later chapter.

## One process can serve many I/O tasks

Node.js does not create one JavaScript thread for every incoming request. JavaScript callbacks run on the event loop, while Node.js and the operating system coordinate asynchronous I/O. Some native tasks use a worker pool. The exact mechanism depends on the API and platform.

This makes Node.js useful for applications that spend time waiting for network or file operations. It does not make long calculations free. A long synchronous callback prevents other JavaScript callbacks from running until it finishes.

Compare a synchronous pause with a timer:

~~~js
console.log('A')

setTimeout(() => {
  console.log('Timer callback')
}, 0)

console.log('B')
~~~

The output is:

~~~text
A
B
Timer callback
~~~

The current JavaScript call stack finishes before the timer callback runs. A zero-millisecond timer means the callback becomes eligible to run after the current work, not that it interrupts the current code.

## Know which directory a path refers to

A relative command-line path is interpreted from the current working directory. **__dirname**, in a CommonJS file, instead points to the folder containing that file.

~~~js
const path = require('node:path')

console.log('Started from:', process.cwd())
console.log('This file is in:', __dirname)
console.log('Data file:', path.join(__dirname, 'data.json'))
~~~

This difference matters when a script is run from a different folder. Later chapters use **node:path** and module-specific URL tools to construct reliable paths.

## Run a small experiment

Run the timer example twice. First, keep the synchronous section short. Then add a loop before the timer:

~~~js
setTimeout(() => console.log('Timer callback'), 0)

const end = Date.now() + 1000
while (Date.now() < end) {
  // This synchronous work keeps JavaScript busy.
}

console.log('Loop finished')
~~~

The timer callback appears after the loop because JavaScript cannot run it while the current callback is occupying the event loop. Do not use this kind of loop in a server request handler.

## Check what you learned

1. What is the difference between JavaScript and Node.js?
2. Which JavaScript engine does Node.js use?
3. Name two APIs that Node.js provides outside the browser.
4. What does **process.version** return?
5. How is **process.cwd()** different from **__dirname**?
6. Why does a zero-millisecond timer not run in the middle of the current call stack?
7. What can happen when a callback performs long synchronous work?
8. Why can Node.js handle many I/O tasks without creating one JavaScript thread per request?

## References

- [Introduction to Node.js](https://nodejs.org/learn/getting-started/introduction-to-nodejs)
- [Node.js documentation](https://nodejs.org/docs/latest/api/)
- [Don't block the event loop](https://nodejs.org/en/learn/asynchronous-work/dont-block-the-event-loop)
- [Process API](https://nodejs.org/api/process.html)