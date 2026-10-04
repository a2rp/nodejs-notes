# 12. Environment configuration and diagnostics

[Back to notes index](../README.md)

| [Previous: Errors, process, and signals](./11-errors-process-and-signals.md) | [Notes index](../README.md) | [Next: Child processes and worker threads](./13-child-processes-and-worker-threads.md) |
| --- | --- | --- |

## Read configuration from the process environment

Environment variables let an application receive settings from the shell or hosting platform without hard-coding them in source files. Node.js exposes them through **process.env**. Values are strings, so parse and validate each setting before using it.

~~~js
const portText = process.env.PORT || '3000'
const port = Number(portText)

if (!Number.isInteger(port) || port < 1 || port > 65535) {
  throw new Error('PORT must be an integer from 1 to 65535')
}

const logLevel = process.env.LOG_LEVEL || 'info'
const allowedLogLevels = new Set(['debug', 'info', 'warn', 'error'])

if (!allowedLogLevels.has(logLevel)) {
  throw new Error('LOG_LEVEL is not supported')
}

console.log({ port, logLevel })
~~~

Validate required values at startup so the program fails clearly before accepting requests. Avoid scattering direct **process.env** reads throughout application logic. A small configuration module gives the rest of the program one validated object.

## Use an environment file for local work

A recent Node.js runtime can load a dotenv-style file from the command line:

~~~sh
node --env-file=.env app.js
~~~

Example **.env**:

~~~dotenv
PORT=3000
LOG_LEVEL=debug
DATABASE_URL=replace-with-a-local-value
~~~

Environment-file values are still strings. Keep real credentials out of source control. Add local secret files to **.gitignore**, and commit an **.env.example** containing only placeholder values so the required settings are clear.

Check the command-line documentation for the exact options supported by the Node.js release used by the project.

## Keep logs useful and safe

Logs help explain what the process did and where a failure occurred. Include stable context such as an operation name, request identifier, and error details that are safe for the intended log destination:

~~~js
console.info('note_saved', { noteId: 42 })
console.error('database_connection_failed', {
  message: error.message,
  name: error.name
})
~~~

Do not log passwords, access tokens, full authorization headers, or full request bodies by default. Logs can be retained and copied to systems with different access controls.

For larger services, use a structured logging format and define log levels. Avoid logging the same failure at every layer if it creates duplicate noise.

## Inspect memory and elapsed time

Node.js exposes process and performance information through built-in APIs. Take measurements around work you want to understand:

~~~js
import { performance } from 'node:perf_hooks'

const startedAt = performance.now()
await runTask()
const elapsedMs = performance.now() - startedAt

console.log('task_finished', { elapsedMs })
console.log('memory', process.memoryUsage())
~~~

**process.memoryUsage()** reports values such as resident memory and JavaScript heap usage. Measurements help form a question; repeat them under representative load before drawing conclusions.

## Use the debugger carefully

Start a local process with the inspector:

~~~sh
node --inspect app.js
~~~

A debugger can pause execution, inspect local values, and follow a call stack. Keep the inspector bound to a trusted local interface. Exposing a debugger port on a public network can give a remote person control over the process.

For startup code, **--inspect-brk** pauses before the application begins. Use it only when that behavior is useful.

## Practice

Create **.env.example** with a sample port and log level. Load a local **.env** file, validate both values at startup, and print a short startup record. Add **.env** to **.gitignore** and confirm that no secret value appears in the Git diff or logs.

## Check what you learned

1. Where does Node.js expose environment variables?
2. Why must configuration values be parsed before use?
3. Why validate required settings during startup?
4. What does the **--env-file** option load?
5. Which environment file can safely be committed as an example?
6. Name two kinds of values that should not be written to logs.
7. What can **process.memoryUsage()** help you inspect?
8. Why should an inspector port not be exposed publicly?

## References

- [Node.js environment variables](https://nodejs.org/api/environment_variables.html)
- [Node.js command-line options](https://nodejs.org/api/cli.html#--env-filefile)
- [Process API](https://nodejs.org/api/process.html)
- [Performance hooks](https://nodejs.org/api/perf_hooks.html)
- [Inspector API](https://nodejs.org/api/inspector.html)