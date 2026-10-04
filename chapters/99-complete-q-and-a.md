# Complete questions and answers

[Back to notes index](../README.md)

| [Previous: All code samples](./98-all-code-samples.md) | [Notes index](../README.md) | Next: End of notes |
| --- | --- | --- |

This appendix answers the review questions from every core chapter. Use it to check understanding, then return to the chapter examples for context.

## 1. Node.js runtime and JavaScript execution

1. **Question:** What is the difference between JavaScript and Node.js?
   **Answer:** Node.js is a JavaScript runtime that adds host APIs and lets JavaScript run outside a browser.

2. **Question:** Which JavaScript engine does Node.js use?
   **Answer:** V8 parses and executes the JavaScript code used by Node.js.

3. **Question:** Name two APIs that Node.js provides outside the browser.
   **Answer:** The built-in filesystem module reads files, and the HTTP module creates servers and makes HTTP requests.

4. **Question:** What does **process.version** return?
   **Answer:** It returns the version string of the Node.js runtime executing the process.

5. **Question:** How is **process.cwd()** different from **__dirname**?
   **Answer:** The current working directory is where the command started; __dirname is the folder containing a CommonJS file.

6. **Question:** Why does a zero-millisecond timer not run in the middle of the current call stack?
   **Answer:** The current call stack must finish before the timer callback can run.

7. **Question:** What can happen when a callback performs long synchronous work?
   **Answer:** Long synchronous work blocks the event loop and delays other callbacks and requests.

8. **Question:** Why can Node.js handle many I/O tasks without creating one JavaScript thread per request?
   **Answer:** Node.js can register asynchronous I/O and run its callbacks when results arrive, without creating one JavaScript thread per request.

## 2. Install Node.js and use the command line

1. **Question:** Which commands confirm that Node.js and npm are available?
   **Answer:** Run node --version and npm --version in a new terminal.

2. **Question:** What happens when you run **node** without a script path?
   **Answer:** Without a script path, Node.js opens its interactive REPL.

3. **Question:** What is **node -e** useful for?
   **Answer:** The -e option evaluates a short JavaScript expression supplied on the command line.

4. **Question:** Which entries in **process.argv** contain the script arguments?
   **Answer:** The executable and script path come first; user-supplied script arguments follow them.

5. **Question:** Why should environment values be parsed and validated?
   **Answer:** Environment values are strings and can be missing, malformed, or outside the accepted range.

6. **Question:** What does exit code zero communicate to the shell?
   **Answer:** Exit code zero reports success to the shell or calling program.

7. **Question:** Why is **process.exitCode** often safer than an immediate process exit?
   **Answer:** Setting process.exitCode lets pending output and cleanup finish before the process exits.

8. **Question:** How can you check whether a JavaScript file parses without running it?
   **Answer:** The --check option parses a JavaScript file without executing it.

## 3. CommonJS modules and project structure

1. **Question:** What problem does splitting code into modules solve?
   **Answer:** Modules keep related code behind file boundaries, making it reusable and easier to understand.

2. **Question:** Which function loads a CommonJS module?
   **Answer:** require() loads a CommonJS module.

3. **Question:** What value does **require()** return?
   **Answer:** require() returns the value assigned to module.exports by the target module.

4. **Question:** How can a module export several named functions?
   **Answer:** Export an object whose properties are the functions or values callers need.

5. **Question:** Why can assigning a new value to **exports** be misleading?
   **Answer:** exports initially points to module.exports; reassigning exports alone does not change module.exports.

6. **Question:** What does the **node:** prefix identify?
   **Answer:** The node: prefix identifies a built-in Node.js module.

7. **Question:** Why does Node.js cache loaded CommonJS modules?
   **Answer:** The cache avoids evaluating a resolved CommonJS module again on every require call.

8. **Question:** What can make circular dependencies difficult to reason about?
   **Answer:** A circular dependency can expose a module before it has assigned all of its exports.

## 4. ECMAScript modules

1. **Question:** Which JavaScript keywords are used to import and export ESM values?
   **Answer:** ESM uses import declarations to load values and export declarations to expose them.

2. **Question:** What does **"type": "module"** change for files ending in **.js**?
   **Answer:** It makes .js files in that package scope use ECMAScript module syntax.

3. **Question:** Which extensions explicitly select ESM and CommonJS?
   **Answer:** The .mjs extension selects ESM, while .cjs selects CommonJS.

4. **Question:** Why should a relative ESM import include its file extension?
   **Answer:** An explicit extension tells Node.js which relative file to resolve.

5. **Question:** How does a named import differ from a default import?
   **Answer:** Named imports use the exported name; a default import chooses a local name for the single default value.

6. **Question:** What does **import.meta.url** represent?
   **Answer:** import.meta.url is the URL of the current ESM module file.

7. **Question:** When is dynamic **import()** useful?
   **Answer:** Dynamic import loads a module conditionally or on demand and returns a Promise.

8. **Question:** How can a package make its module mode clear?
   **Answer:** Set the package type and file extensions consistently so the module mode is clear.

## 5. npm, package.json, and dependencies

1. **Question:** What information does **package.json** keep for a project?
   **Answer:** package.json records project metadata, module mode, scripts, and dependency declarations.

2. **Question:** What is the purpose of a package script?
   **Answer:** A script gives a repeatable project command such as start or test.

3. **Question:** Where does npm record production dependencies?
   **Answer:** Runtime packages used by the application belong in dependencies.

4. **Question:** When does a package belong in **devDependencies**?
   **Answer:** Packages used only for local development, testing, or building belong in devDependencies.

5. **Question:** What does a semantic version range communicate?
   **Answer:** A version range specifies which package releases can satisfy the declared dependency.

6. **Question:** Why should an application commit **package-lock.json**?
   **Answer:** The lockfile records the resolved dependency tree so installs can reproduce it.

7. **Question:** How does **npm ci** differ from **npm install** when using a lockfile?
   **Answer:** npm ci installs the existing locked tree and does not rewrite the manifest or lockfile; npm install can update them.

8. **Question:** Why should an audit report be reviewed before forcing dependency changes?
   **Answer:** Review advisories and compatibility before forcing updates because a major change can break the application.

## 6. Asynchronous JavaScript and the event loop

1. **Question:** What is the difference between synchronous and asynchronous work?
   **Answer:** Synchronous work completes in the current call; asynchronous work can finish later while other JavaScript runs.

2. **Question:** What information does an error-first callback provide?
   **Answer:** An error-first callback receives an error first and a result in a later argument on success.

3. **Question:** What are the two possible outcomes of a Promise?
   **Answer:** A Promise can fulfill with a value or reject with a reason.

4. **Question:** What does an **async** function return?
   **Answer:** An async function always returns a Promise.

5. **Question:** What does **await** pause while a Promise is pending?
   **Answer:** await pauses the current async function until its Promise settles, while the event loop can run other work.

6. **Question:** When is **Promise.all** a good fit?
   **Answer:** Promise.all is useful when several independent operations should start together and all results are required.

7. **Question:** Does **Promise.all** cancel the other operations after one rejects?
   **Answer:** No. Rejection does not automatically cancel the other operations that have already started.

8. **Question:** How can long synchronous JavaScript work affect an application?
   **Answer:** Long synchronous JavaScript work blocks the event loop and delays unrelated callbacks.

## 7. Events and EventEmitter

1. **Question:** What is an event useful for communicating?
   **Answer:** An event names something that happened so other parts of the process can react.

2. **Question:** Which built-in module provides EventEmitter?
   **Answer:** The node:events module provides EventEmitter.

3. **Question:** What is the difference between **on** and **once**?
   **Answer:** on registers for repeated events; once removes itself after the first matching event.

4. **Question:** In what order are listeners called?
   **Answer:** Listeners run synchronously in the order they were registered.

5. **Question:** Why should a listener avoid expensive synchronous work?
   **Answer:** Expensive synchronous work in a listener delays the emit call and other event-loop work.

6. **Question:** What happens when **error** is emitted without an error listener?
   **Answer:** Emitting error without an error listener throws the error.

7. **Question:** Why must **off** receive the same function reference that was registered?
   **Answer:** off must receive the same function object that was passed to on.

8. **Question:** Does an EventEmitter send events to another Node.js process?
   **Answer:** No. EventEmitter is local to one process and does not provide cross-process delivery.

## 8. Files, paths, and URLs

1. **Question:** Which module exposes promise-based file operations?
   **Answer:** node:fs/promises provides Promise-based filesystem operations.

2. **Question:** Why are asynchronous file APIs preferred in server request handlers?
   **Answer:** They avoid blocking JavaScript while a server waits for filesystem work.

3. **Question:** When should **path.join** or **path.resolve** be used?
   **Answer:** Use path.join or path.resolve to combine filesystem path pieces safely across operating systems.

4. **Question:** How is a filesystem path different from a web URL?
   **Answer:** A filesystem path locates data on a machine; a URL identifies a resource and may contain query parameters.

5. **Question:** Why is a string prefix check insufficient for path containment?
   **Answer:** After resolving a candidate, compare its relative path to the base boundary; a string prefix can match a sibling folder.

6. **Question:** Which URL property gives access to query parameters?
   **Answer:** The searchParams property provides access to query values.

7. **Question:** How can ESM code convert its file URL to a filesystem path?
   **Answer:** Use fileURLToPath to convert a file URL such as import.meta.url into a filesystem path.

8. **Question:** What extra risk can symbolic links create for a directory boundary check?
   **Answer:** A symbolic link inside the allowed directory may point to a target outside that directory.

## 9. Buffers and streams

1. **Question:** What kind of data does a Buffer hold?
   **Answer:** A Buffer stores a fixed-size sequence of bytes.

2. **Question:** Why can a string's character count differ from its byte length?
   **Answer:** Text encoding determines how characters become bytes, so one character can use multiple bytes.

3. **Question:** What does a readable stream provide?
   **Answer:** A readable stream provides data to a consumer.

4. **Question:** What does a Transform stream do?
   **Answer:** A Transform stream reads input, changes it, and produces output.

5. **Question:** Why can streams use less memory for large files?
   **Answer:** A stream processes chunks rather than requiring the whole large file in memory at once.

6. **Question:** What problem does backpressure help control?
   **Answer:** Backpressure slows a producer when a consumer cannot accept data quickly enough.

7. **Question:** What does a false return from **write()** mean?
   **Answer:** A false return means stop writing for now and wait for the writable stream to signal drain.

8. **Question:** Why is **pipeline** useful when connecting streams?
   **Answer:** pipeline connects stream stages and coordinates completion, backpressure, and error propagation.

## 10. HTTP servers and web requests

1. **Question:** Which built-in module creates an HTTP server?
   **Answer:** node:http provides the HTTP server and client APIs.

2. **Question:** What details should a route check besides the URL path?
   **Answer:** A route should consider both the request method and parsed path.

3. **Question:** Why should request URLs be parsed with the URL class?
   **Answer:** The URL class handles path encoding and query values without fragile string splitting.

4. **Question:** Why must a JSON request body have a size limit?
   **Answer:** A size limit bounds memory use when a client sends a very large body.

5. **Question:** What should the server validate after parsing JSON?
   **Answer:** Validate content type, data shape, required fields, and allowed value ranges.

6. **Question:** Which status code describes a newly created resource?
   **Answer:** 201 Created indicates that a resource was successfully created.

7. **Question:** Why should an HTTP client inspect **response.ok**?
   **Answer:** A response can have an error status even when the HTTP exchange itself completed, so check response.ok or status.

8. **Question:** What information should a server avoid returning in an error response?
   **Answer:** Do not return stack traces, secrets, or internal paths to an untrusted client.

## 11. Errors, process, and signals

1. **Question:** Where should an expected input error usually be checked?
   **Answer:** Check expected invalid input at the boundary before starting work.

2. **Question:** How can an awaited rejection be handled?
   **Answer:** Use try/catch around await, or attach a catch handler to the Promise.

3. **Question:** What does an error cause preserve?
   **Answer:** The cause property preserves the original error that led to a higher-level error.

4. **Question:** Which layer should decide how a failure is shown to a user?
   **Answer:** The layer that understands the user-facing operation should decide how to report or translate failure.

5. **Question:** What does a nonzero process exit code signal?
   **Answer:** A nonzero code tells the shell or caller that the process failed.

6. **Question:** Why can immediate process exit lose pending output or cleanup?
   **Answer:** Immediate exit can stop pending output and resource cleanup.

7. **Question:** What is the purpose of graceful server shutdown?
   **Answer:** Graceful shutdown stops accepting new connections and gives active work a chance to finish while resources close.

8. **Question:** Why should a shutdown function safely handle repeated signals?
   **Answer:** An idempotent shutdown handler avoids starting cleanup multiple times when signals repeat.

## 12. Environment configuration and diagnostics

1. **Question:** Where does Node.js expose environment variables?
   **Answer:** Node.js exposes inherited environment variables through process.env.

2. **Question:** Why must configuration values be parsed before use?
   **Answer:** They are strings, so parse and validate them before using them as numbers, flags, or structured values.

3. **Question:** Why validate required settings during startup?
   **Answer:** Startup validation catches missing or invalid configuration before the application accepts work.

4. **Question:** What does the **--env-file** option load?
   **Answer:** --env-file loads dotenv-style values into the process environment before the application runs.

5. **Question:** Which environment file can safely be committed as an example?
   **Answer:** Commit an .env.example containing placeholder values and required setting names.

6. **Question:** Name two kinds of values that should not be written to logs.
   **Answer:** Do not log passwords, tokens, authorization headers, or sensitive request bodies.

7. **Question:** What can **process.memoryUsage()** help you inspect?
   **Answer:** process.memoryUsage reports process memory measures such as resident memory and JavaScript heap use.

8. **Question:** Why should an inspector port not be exposed publicly?
   **Answer:** A public inspector port can allow a remote user to control or inspect the running process.

## 13. Child processes and worker threads

1. **Question:** What work is a child process useful for?
   **Answer:** A child process can run another executable or isolate work behind a separate process boundary.

2. **Question:** Why pass arguments as an array to **spawn**?
   **Answer:** An argument array keeps the executable arguments separate without constructing one shell command string.

3. **Question:** What risk can occur when untrusted text is passed to a shell?
   **Answer:** Untrusted shell text can inject additional commands or alter the intended command.

4. **Question:** Which child process event reports that stdio streams have closed?
   **Answer:** The close event reports that the process and its stdio streams have closed.

5. **Question:** When is **execFile** useful?
   **Answer:** execFile is useful for running a program and collecting bounded output without a shell.

6. **Question:** What kind of JavaScript work may benefit from a worker thread?
   **Answer:** CPU-heavy JavaScript may benefit because it would otherwise keep the main event loop busy.

7. **Question:** Why should a service bound the number of workers it creates?
   **Answer:** An unbounded worker count can exhaust CPU and memory resources.

8. **Question:** How does a worker thread differ from a child process?
   **Answer:** A child is a separate process; a worker is a separate JavaScript thread within the same process.

## 14. Testing with the built-in test runner

1. **Question:** Which built-in module provides the test runner?
   **Answer:** node:test provides the built-in test runner.

2. **Question:** Which built-in module provides strict assertions?
   **Answer:** node:assert/strict provides strict assertion methods.

3. **Question:** What does **node --test** do?
   **Answer:** node --test discovers and runs test files.

4. **Question:** Which assertion checks that synchronous code throws?
   **Answer:** assert.throws checks that synchronous code throws.

5. **Question:** Which assertion checks that a Promise rejects?
   **Answer:** assert.rejects checks that a Promise rejects.

6. **Question:** Why should an async test await its assertions?
   **Answer:** Await the assertion so the runner knows the asynchronous expectation has completed.

7. **Question:** What kinds of cases should a small function test cover?
   **Answer:** Test ordinary inputs, boundaries, invalid values, and observable behavior.

8. **Question:** Why should a test close the server or other resources it opens?
   **Answer:** Open resources can keep the process alive and leak state into other tests, so close them.

## 15. Security, performance, and production practices

1. **Question:** Which values should be validated at an application boundary?
   **Answer:** Validate command arguments, environment configuration, request headers, URL values, and body data at boundaries.

2. **Question:** Why should request bodies have size limits?
   **Answer:** A body limit bounds memory and the amount of work a client can force the service to do.

3. **Question:** Which details should not be returned to an untrusted client?
   **Answer:** Keep stack traces, internal paths, credentials, and private implementation details out of client responses.

4. **Question:** How can large synchronous work affect a Node.js service?
   **Answer:** Large synchronous work blocks the event loop and delays other requests.

5. **Question:** What does a dependency lockfile help reproduce?
   **Answer:** A lockfile records the resolved dependency tree for repeatable installs.

6. **Question:** Why does a clean audit report not prove an application is secure?
   **Answer:** An audit checks known dependency advisories but does not prove application logic is secure.

7. **Question:** Why should a service use the least permissions it needs?
   **Answer:** Least privilege limits the damage possible if a process or dependency is compromised.

8. **Question:** What should an application do when its host sends a termination signal?
   **Answer:** Stop accepting new work and close servers, connections, and other resources cleanly.

## 16. Build and test a small Node.js service

1. **Question:** Which routes does the service expose?
   **Answer:** The service exposes GET /health, GET /notes, and POST /notes.

2. **Question:** Why does **createAppServer** return a server without starting it?
   **Answer:** Returning the server lets tests and callers start it on a chosen port without starting it during module import.

3. **Question:** How does the body reader limit retained data?
   **Answer:** It stops retaining additional chunks after the configured byte limit, while consuming the request.

4. **Question:** Which status code is returned when a note is created?
   **Answer:** 201 Created is returned for a valid newly created note.

5. **Question:** What does the **Allow** header communicate for **405**?
   **Answer:** The Allow header tells clients which methods are accepted for that resource.

6. **Question:** Why does the test listen on port zero?
   **Answer:** Port zero asks the operating system to select an available ephemeral port.

7. **Question:** Why does the test close its server after finishing?
   **Answer:** Closing the server releases its socket so the test process can finish cleanly.

8. **Question:** Which parts must change before using this in-memory example for durable production data?
   **Answer:** Replace the in-memory array with durable storage and add the authentication, operations, and deployment behavior the service needs.

