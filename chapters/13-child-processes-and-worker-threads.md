# 13. Child processes and worker threads

[Back to notes index](../README.md)

| [Previous: Environment configuration and diagnostics](./12-environment-configuration-and-diagnostics.md) | [Notes index](../README.md) | [Next: Testing with the built-in test runner](./14-testing-with-built-in-test-runner.md) |
| --- | --- | --- |

## Start another process

A child process runs separately from the current Node.js process. It is useful when an application needs to run another program or isolate work behind a process boundary.

Use **spawn** with an executable and an argument array:

~~~js
import { spawn } from 'node:child_process'

const child = spawn(
  process.execPath,
  ['-e', "console.log('child process started')"],
  { stdio: ['ignore', 'pipe', 'inherit'] }
)

child.stdout.setEncoding('utf8')
child.stdout.on('data', (text) => {
  console.log('child output:', text.trim())
})

child.on('error', (error) => {
  console.error('Could not start child:', error.message)
})

child.on('close', (code, signal) => {
  console.log('Child closed:', { code, signal })
})
~~~

**process.execPath** identifies the current Node executable. An argument array keeps each value separate. Avoid building a shell command by concatenating untrusted input. If a shell is genuinely required, validate every argument and understand the injection risk.

The **close** event reports that the process has closed and its stdio streams have closed. Check its exit code before treating the command as successful.

## Pick the right child process API

**spawn** is suited to streaming output from a long-running command. **execFile** runs a program with arguments and collects its output, which is convenient when the output is bounded. **exec** runs a command through a shell and should not receive untrusted command text.

Do not use synchronous child-process calls in a request handler. They block the event loop while the child runs.

## Move CPU-heavy JavaScript to a worker

Worker threads run JavaScript on another thread within the same process. They can help with CPU-heavy work that would otherwise occupy the main JavaScript thread. They are usually unnecessary for ordinary asynchronous file or network I/O.

Create **sum-worker.js**:

~~~js
import { parentPort, workerData } from 'node:worker_threads'

let total = 0
for (let value = 1; value <= workerData.limit; value += 1) {
  total += value
}

parentPort.postMessage(total)
~~~

Create **run-sum.js**:

~~~js
import { Worker } from 'node:worker_threads'

function sumInWorker(limit) {
  return new Promise((resolve, reject) => {
    const worker = new Worker(new URL('./sum-worker.js', import.meta.url), {
      workerData: { limit }
    })

    worker.once('message', resolve)
    worker.once('error', reject)
    worker.once('exit', (code) => {
      if (code !== 0) {
        reject(new Error('Worker stopped with exit code ' + code))
      }
    })
  })
}

console.log(await sumInWorker(1000000))
~~~

Worker messages are copied between threads unless a supported transferable or shared-memory value is used. Starting a worker has a cost, so a server that handles repeated CPU tasks should reuse a bounded worker pool instead of creating unlimited workers.

## Compare the boundaries

A child process has its own process lifecycle and communicates through configured streams or an inter-process channel. A worker thread shares the parent process but runs JavaScript on a separate thread. Choose based on isolation, the program to run, data transfer, and lifecycle needs.

Do not use either technique to hide an unbounded workload. Limit concurrent children or workers, validate inputs, and handle startup failures and nonzero exits.

## Practice

Spawn the current Node.js executable with a short script and capture its output. Then move a CPU-only calculation into a worker file. Compare the result and note that moving work to another thread adds startup and message-transfer costs.

## Check what you learned

1. What work is a child process useful for?
2. Why pass arguments as an array to **spawn**?
3. What risk can occur when untrusted text is passed to a shell?
4. Which child process event reports that stdio streams have closed?
5. When is **execFile** useful?
6. What kind of JavaScript work may benefit from a worker thread?
7. Why should a service bound the number of workers it creates?
8. How does a worker thread differ from a child process?

## References

- [Child processes API](https://nodejs.org/api/child_process.html)
- [Worker threads API](https://nodejs.org/api/worker_threads.html)
- [Don't block the event loop](https://nodejs.org/en/learn/asynchronous-work/dont-block-the-event-loop)