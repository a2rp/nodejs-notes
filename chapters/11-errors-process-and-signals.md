# 11. Errors, process, and signals

[Back to notes index](../README.md)

| [Previous: HTTP servers and web requests](./10-http-servers-and-web-requests.md) | [Notes index](../README.md) | [Next: Environment configuration and diagnostics](./12-environment-configuration-and-diagnostics.md) |
| --- | --- | --- |

## Treat errors as part of normal control flow

Files can be missing, network calls can fail, and input can be invalid. Decide where each failure should be handled. Add context where it helps, and let a caller handle the error when that caller can choose the right response.

Use **try/catch** around synchronous code that can throw:

~~~js
try {
  const value = JSON.parse('not json')
  console.log(value)
} catch (error) {
  console.error('Could not parse input:', error.message)
}
~~~

For asynchronous work, use **try/catch** around **await** or attach a **catch** handler:

~~~js
try {
  const text = await readFile('./missing.txt', 'utf8')
  console.log(text)
} catch (error) {
  console.error('Could not load file:', error.message)
}
~~~

Do not leave a Promise rejection without a clear owner.

## Preserve useful error context

When an operation fails, include context without hiding the original cause:

~~~js
async function loadSettings(filePath) {
  try {
    return JSON.parse(await readFile(filePath, 'utf8'))
  } catch (cause) {
    throw new Error('Could not load settings', { cause })
  }
}
~~~

A caller can report the higher-level failure while inspecting **error.cause** in a local log. Avoid returning stack traces, paths, or credentials to an untrusted HTTP client.

## Return errors at the boundary

A lower-level helper should report failure by throwing or returning a rejected Promise. The layer that understands the user-facing context can convert it into an HTTP response, command-line message, or retry decision.

~~~js
try {
  const result = await saveRecord(input)
  sendJson(response, 201, result)
} catch (error) {
  console.error('Saving record failed:', error)
  sendJson(response, 500, { error: 'Could not save record' })
}
~~~

Validate expected input errors before starting expensive work. Do not use exceptions as a substitute for ordinary branching when an invalid value is expected and easy to check.

## Set the process exit code

A command-line program can report failure with a nonzero exit code:

~~~js
try {
  await run()
} catch (error) {
  console.error(error.message)
  process.exitCode = 1
}
~~~

Setting **process.exitCode** lets Node.js finish pending output and cleanup. An immediate **process.exit()** stops the process even if work is still pending, so reserve it for a deliberate last-resort shutdown path.

## Close a server when the process is asked to stop

A hosting platform may send **SIGTERM** during shutdown. A terminal user can send **SIGINT** with Ctrl+C. Close network servers and other resources so active work can finish:

~~~js
let shuttingDown = false

function shutdown(signal) {
  if (shuttingDown) return
  shuttingDown = true

  console.log('Received', signal, 'starting shutdown')

  server.close((error) => {
    if (error) {
      console.error('Server shutdown failed:', error)
      process.exitCode = 1
    }
  })
}

process.on('SIGINT', () => shutdown('SIGINT'))
process.on('SIGTERM', () => shutdown('SIGTERM'))
~~~

This example assumes **server** is an HTTP server. An application with database pools, workers, or other resources should close each one and place a time limit on shutdown. Keep the handler idempotent so repeated signals do not start multiple cleanup sequences.

Do not try to continue normal service after a fatal process-level failure. Record enough information for diagnosis, then let a process manager restart the application if appropriate.

## Practice

Make a command-line script that reads a JSON file. Show a short message for a missing path, a parse error, and a valid file. Verify that failure sets the exit code. Add a shutdown handler to a local HTTP server and stop it with Ctrl+C.

## Check what you learned

1. Where should an expected input error usually be checked?
2. How can an awaited rejection be handled?
3. What does an error cause preserve?
4. Which layer should decide how a failure is shown to a user?
5. What does a nonzero process exit code signal?
6. Why can immediate process exit lose pending output or cleanup?
7. What is the purpose of graceful server shutdown?
8. Why should a shutdown function safely handle repeated signals?

## References

- [Errors API](https://nodejs.org/api/errors.html)
- [Process API](https://nodejs.org/api/process.html)
- [HTTP server close](https://nodejs.org/api/http.html#serverclosecallback)
- [Node.js security best practices](https://nodejs.org/en/learn/getting-started/security-best-practices)