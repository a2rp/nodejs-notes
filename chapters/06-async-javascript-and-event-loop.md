# 6. Asynchronous JavaScript and the event loop

[Back to notes index](../README.md)

| [Previous: npm, package.json, and dependencies](./05-npm-package-json-and-dependencies.md) | [Notes index](../README.md) | [Next: Events and EventEmitter](./07-events-and-eventemitter.md) |
| --- | --- | --- |

## Synchronous work and waiting

Synchronous code runs to completion in order. If a function has to wait for a network response or file operation before returning, it can hold up later JavaScript work.

Node.js APIs often offer asynchronous forms. They start an operation and let JavaScript continue. When the operation completes, Node.js schedules the associated callback or promise continuation.

~~~js
console.log('First')

setTimeout(() => {
  console.log('Later')
}, 0)

console.log('Second')
~~~

The first two lines print before the timer callback. A timer schedules work; it does not interrupt the current JavaScript stack.

## Callback style

A callback is a function passed to another function to run later. Many older Node.js APIs use the error-first convention: the first callback argument is an error, and the next argument carries a result.

~~~js
const fs = require('node:fs')

fs.readFile('notes.txt', 'utf8', (error, text) => {
  if (error) {
    console.error('Read failed:', error.message)
    return
  }

  console.log(text)
})
~~~

Handle the error before using the result. Nested callbacks can become difficult to follow when each operation depends on the result of the previous one.

## Promise style

A Promise represents work that may finish later, either successfully or with an error. The Promise-based filesystem API can be used with **then** and **catch**:

~~~js
const { readFile } = require('node:fs/promises')

readFile('notes.txt', 'utf8')
  .then((text) => console.log(text))
  .catch((error) => {
    console.error('Read failed:', error.message)
    process.exitCode = 1
  })
~~~

A rejected Promise must be handled. Return promises from helper functions so the caller can compose them and observe failures.

## async and await

An **async** function always returns a Promise. **await** pauses that function until the awaited Promise settles, while other JavaScript work can continue.

~~~js
import { readFile } from 'node:fs/promises'

async function readNotes(filePath) {
  try {
    const text = await readFile(filePath, 'utf8')
    return text
  } catch (error) {
    throw new Error('Could not read notes', { cause: error })
  }
}

try {
  console.log(await readNotes('./notes.txt'))
} catch (error) {
  console.error(error.message, error.cause?.message)
  process.exitCode = 1
}
~~~

The outer **try/catch** handles a rejected result from **readNotes**. The **cause** property keeps the original error available for debugging.

## Run independent work in parallel

When two operations do not depend on each other, starting them together can avoid waiting for each one in sequence:

~~~js
import { readFile } from 'node:fs/promises'

const [first, second] = await Promise.all([
  readFile('./first.txt', 'utf8'),
  readFile('./second.txt', 'utf8')
])

console.log(first.length, second.length)
~~~

**Promise.all** rejects when one input Promise rejects. Other started operations are not automatically cancelled. If partial results are useful, learn the behavior of **Promise.allSettled** and handle each result.

When the second operation needs the first result, use **await** sequentially. Parallelism is only correct when the operations are independent.

## The event loop and responsive work

The event loop allows Node.js to coordinate callbacks and asynchronous operations. JavaScript callbacks still run one at a time on the main JavaScript thread. A large synchronous loop, expensive parsing task, or synchronous file operation can delay timers and unrelated request callbacks.

Keep each callback small. For CPU-heavy work, measure the cost and consider partitioning the work or moving it to a worker thread. Asynchronous I/O does not make CPU-bound JavaScript asynchronous by itself.

## Practice

Read two small local text files at the same time with **Promise.all**. Then change one path to a missing file and observe how the rejection reaches **catch**. Next, move the second read below the first with **await** and explain whether the operations now run sequentially.

## Check what you learned

1. What is the difference between synchronous and asynchronous work?
2. What information does an error-first callback provide?
3. What are the two possible outcomes of a Promise?
4. What does an **async** function return?
5. What does **await** pause while a Promise is pending?
6. When is **Promise.all** a good fit?
7. Does **Promise.all** cancel the other operations after one rejects?
8. How can long synchronous JavaScript work affect an application?

## References

- [Node.js asynchronous work](https://nodejs.org/en/learn/asynchronous-work)
- [Don't block the event loop](https://nodejs.org/en/learn/asynchronous-work/dont-block-the-event-loop)
- [File system promises API](https://nodejs.org/api/fs.html#promises-api)
- [Errors](https://nodejs.org/api/errors.html)