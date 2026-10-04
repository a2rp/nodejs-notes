# All code samples

[Back to notes index](../README.md)

| [Previous: Build and test a small Node.js service](./16-build-a-small-nodejs-service.md) | [Notes index](../README.md) | [Next: Complete questions and answers](./99-complete-q-and-a.md) |
| --- | --- | --- |

This appendix gathers the runnable snippets from the core chapters in chapter order. Read each example with its chapter explanation when surrounding setup or context is needed.

## 1. Node.js runtime and JavaScript execution

### Sample 1 (js)

~~~~js
console.log('Hello from Node.js')
console.log('Node version:', process.version)
console.log('Current folder:', process.cwd())
~~~~

### Sample 2 (sh)

~~~~sh
node hello.js
~~~~

### Sample 3 (js)

~~~~js
const path = require('node:path')
const os = require('node:os')

console.log('Home folder:', os.homedir())
console.log('Joined path:', path.join('notes', 'chapter.md'))
~~~~

### Sample 4 (js)

~~~~js
console.log('A')

setTimeout(() => {
  console.log('Timer callback')
}, 0)

console.log('B')
~~~~

### Sample 5 (text)

~~~~text
A
B
Timer callback
~~~~

### Sample 6 (js)

~~~~js
const path = require('node:path')

console.log('Started from:', process.cwd())
console.log('This file is in:', __dirname)
console.log('Data file:', path.join(__dirname, 'data.json'))
~~~~

### Sample 7 (js)

~~~~js
setTimeout(() => console.log('Timer callback'), 0)

const end = Date.now() + 1000
while (Date.now() < end) {
  // This synchronous work keeps JavaScript busy.
}

console.log('Loop finished')
~~~~

## 2. Install Node.js and use the command line

### Sample 1 (sh)

~~~~sh
node --version
npm --version
~~~~

### Sample 2 (sh)

~~~~sh
node app.js
~~~~

### Sample 3 (sh)

~~~~sh
node -e "console.log(process.platform)"
~~~~

### Sample 4 (js)

~~~~js
const name = process.argv[2] || 'friend'
console.log('Hello, ' + name)
~~~~

### Sample 5 (sh)

~~~~sh
node greet.js Ashish
~~~~

### Sample 6 (js)

~~~~js
console.log(process.argv)
~~~~

### Sample 7 (js)

~~~~js
const portText = process.env.PORT || '3000'
const port = Number(portText)

if (!Number.isInteger(port) || port < 1 || port > 65535) {
  throw new Error('PORT must be a valid TCP port')
}

console.log('Listening port:', port)
~~~~

### Sample 8 (js)

~~~~js
const input = process.argv[2]

if (!input) {
  console.error('Usage: node check.js <value>')
  process.exitCode = 1
} else {
  console.log('Received:', input)
}
~~~~

### Sample 9 (sh)

~~~~sh
node --help
~~~~

### Sample 10 (sh)

~~~~sh
node --check app.js
~~~~

## 3. CommonJS modules and project structure

### Sample 1 (text)

~~~~text
price-check/
  app.cjs
  price.cjs
~~~~

### Sample 2 (js)

~~~~js
function addTax(amount, rate) {
  if (!Number.isFinite(amount) || amount < 0) {
    throw new TypeError('amount must be a non-negative number')
  }

  if (!Number.isFinite(rate) || rate < 0 || rate > 1) {
    throw new RangeError('rate must be between 0 and 1')
  }

  return amount + amount * rate
}

module.exports = { addTax }
~~~~

### Sample 3 (js)

~~~~js
const { addTax } = require('./price.cjs')

console.log(addTax(100, 0.1))
~~~~

### Sample 4 (js)

~~~~js
module.exports = function add(a, b) {
  return a + b
}
~~~~

### Sample 5 (js)

~~~~js
const add = require('./add.cjs')
console.log(add(2, 3))
~~~~

### Sample 6 (js)

~~~~js
exports.add = (a, b) => a + b
~~~~

### Sample 7 (js)

~~~~js
const { readFile } = require('node:fs/promises')
const path = require('node:path')

async function showFile(fileName) {
  const filePath = path.resolve(fileName)
  const text = await readFile(filePath, 'utf8')
  console.log(text)
}

showFile('./notes.txt').catch((error) => {
  console.error('Could not read notes:', error.message)
  process.exitCode = 1
})
~~~~

## 4. ECMAScript modules

### Sample 1 (json)

~~~~json
{
  "name": "price-check",
  "private": true,
  "type": "module"
}
~~~~

### Sample 2 (js)

~~~~js
export function addTax(amount, rate) {
  if (!Number.isFinite(amount) || amount < 0) {
    throw new TypeError('amount must be a non-negative number')
  }

  if (!Number.isFinite(rate) || rate < 0 || rate > 1) {
    throw new RangeError('rate must be between 0 and 1')
  }

  return amount + amount * rate
}
~~~~

### Sample 3 (js)

~~~~js
import { addTax } from './price.js'

console.log(addTax(100, 0.1))
~~~~

### Sample 4 (js)

~~~~js
export default function formatPrice(amount) {
  return amount.toFixed(2)
}
~~~~

### Sample 5 (js)

~~~~js
import formatPrice from './format-price.js'

console.log(formatPrice(12.5))
~~~~

### Sample 6 (js)

~~~~js
import { readFile } from 'node:fs/promises'
import path from 'node:path'

const filePath = path.resolve('notes.txt')
const text = await readFile(filePath, 'utf8')
console.log(text)
~~~~

### Sample 7 (js)

~~~~js
import { readFile } from 'node:fs/promises'
import { fileURLToPath } from 'node:url'

const currentFile = fileURLToPath(import.meta.url)
const currentDirectory = fileURLToPath(new URL('.', import.meta.url))
const text = await readFile(new URL('./notes.txt', import.meta.url), 'utf8')

console.log(currentFile)
console.log(currentDirectory)
console.log(text)
~~~~

### Sample 8 (js)

~~~~js
const selectedModule = './price.js'

const { addTax } = await import(selectedModule)
console.log(addTax(50, 0.08))
~~~~

## 5. npm, package.json, and dependencies

### Sample 1 (sh)

~~~~sh
mkdir nodejs-practice
cd nodejs-practice
npm init -y
~~~~

### Sample 2 (json)

~~~~json
{
  "name": "nodejs-practice",
  "version": "1.0.0",
  "private": true,
  "type": "module",
  "scripts": {
    "start": "node app.js",
    "test": "node --test"
  }
}
~~~~

### Sample 3 (sh)

~~~~sh
npm run
npm run start
npm test
~~~~

### Sample 4 (sh)

~~~~sh
npm install package-name
npm install --save-dev another-package
~~~~

### Sample 5 (js)

~~~~js
import { something } from 'package-name'
~~~~

### Sample 6 (sh)

~~~~sh
npm ci
~~~~

### Sample 7 (sh)

~~~~sh
npm ls --depth=0
~~~~

### Sample 8 (sh)

~~~~sh
npm audit
~~~~

## 6. Asynchronous JavaScript and the event loop

### Sample 1 (js)

~~~~js
console.log('First')

setTimeout(() => {
  console.log('Later')
}, 0)

console.log('Second')
~~~~

### Sample 2 (js)

~~~~js
const fs = require('node:fs')

fs.readFile('notes.txt', 'utf8', (error, text) => {
  if (error) {
    console.error('Read failed:', error.message)
    return
  }

  console.log(text)
})
~~~~

### Sample 3 (js)

~~~~js
const { readFile } = require('node:fs/promises')

readFile('notes.txt', 'utf8')
  .then((text) => console.log(text))
  .catch((error) => {
    console.error('Read failed:', error.message)
    process.exitCode = 1
  })
~~~~

### Sample 4 (js)

~~~~js
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
~~~~

### Sample 5 (js)

~~~~js
import { readFile } from 'node:fs/promises'

const [first, second] = await Promise.all([
  readFile('./first.txt', 'utf8'),
  readFile('./second.txt', 'utf8')
])

console.log(first.length, second.length)
~~~~

## 7. Events and EventEmitter

### Sample 1 (js)

~~~~js
const { EventEmitter } = require('node:events')

const orderEvents = new EventEmitter()

orderEvents.on('created', (order) => {
  console.log('New order:', order.id)
})

orderEvents.emit('created', { id: 'order-104', total: 25 })
~~~~

### Sample 2 (js)

~~~~js
orderEvents.once('ready', () => {
  console.log('The first ready event was received')
})

orderEvents.emit('ready')
orderEvents.emit('ready')
~~~~

### Sample 3 (js)

~~~~js
function logOrder(order) {
  console.log('Order:', order.id)
}

orderEvents.on('created', logOrder)
orderEvents.emit('created', { id: 'order-105' })
orderEvents.off('created', logOrder)
~~~~

### Sample 4 (js)

~~~~js
orderEvents.on('error', (error) => {
  console.error('Order event failed:', error.message)
})

orderEvents.emit('error', new Error('Payment was declined'))
~~~~

## 8. Files, paths, and URLs

### Sample 1 (js)

~~~~js
import { mkdir, readFile, writeFile } from 'node:fs/promises'
import path from 'node:path'

const dataDirectory = path.resolve('data')
const filePath = path.join(dataDirectory, 'note.txt')

await mkdir(dataDirectory, { recursive: true })
await writeFile(filePath, 'Practice file operations\n', 'utf8')

const text = await readFile(filePath, 'utf8')
console.log(text)
~~~~

### Sample 2 (js)

~~~~js
import path from 'node:path'

const filePath = path.join('data', 'notes', 'today.txt')
console.log(filePath)
~~~~

### Sample 3 (js)

~~~~js
import path from 'node:path'

function resolveInside(baseDirectory, requestedPath) {
  const base = path.resolve(baseDirectory)
  const target = path.resolve(base, requestedPath)
  const relative = path.relative(base, target)

  if (
    relative === '..' ||
    relative.startsWith('..' + path.sep) ||
    path.isAbsolute(relative)
  ) {
    throw new Error('Path must stay inside the data directory')
  }

  return target
}

console.log(resolveInside('./data', 'notes/today.txt'))
~~~~

### Sample 4 (js)

~~~~js
const requestUrl = new URL(
  '/search?topic=streams&page=2',
  'http://localhost:3000'
)

console.log(requestUrl.pathname)
console.log(requestUrl.searchParams.get('topic'))
console.log(requestUrl.searchParams.get('page'))
~~~~

### Sample 5 (js)

~~~~js
import { fileURLToPath } from 'node:url'
import path from 'node:path'

const currentFile = fileURLToPath(import.meta.url)
const currentDirectory = path.dirname(currentFile)

console.log(currentDirectory)
~~~~

### Sample 6 (js)

~~~~js
import { readFile } from 'node:fs/promises'

const text = await readFile(new URL('./message.txt', import.meta.url), 'utf8')
console.log(text)
~~~~

## 9. Buffers and streams

### Sample 1 (js)

~~~~js
const greeting = Buffer.from('hello', 'utf8')

console.log(greeting)
console.log(greeting.toString('utf8'))
console.log(Buffer.byteLength('hello', 'utf8'))
~~~~

### Sample 2 (js)

~~~~js
import { createReadStream, createWriteStream } from 'node:fs'
import { pipeline } from 'node:stream/promises'

try {
  await pipeline(
    createReadStream('./input.txt'),
    createWriteStream('./copy.txt')
  )
  console.log('Copy complete')
} catch (error) {
  console.error('Copy failed:', error.message)
  process.exitCode = 1
}
~~~~

### Sample 3 (js)

~~~~js
import { createReadStream, createWriteStream } from 'node:fs'
import { Transform } from 'node:stream'
import { pipeline } from 'node:stream/promises'

const uppercase = new Transform({
  transform(chunk, encoding, callback) {
    callback(null, chunk.toString('utf8').toUpperCase())
  }
})

await pipeline(
  createReadStream('./input.txt'),
  uppercase,
  createWriteStream('./uppercase.txt')
)
~~~~

### Sample 4 (js)

~~~~js
import { once } from 'node:events'

async function writeChunks(writable, chunks) {
  for (const chunk of chunks) {
    if (!writable.write(chunk)) {
      await once(writable, 'drain')
    }
  }

  writable.end()
  await once(writable, 'finish')
}
~~~~

## 10. HTTP servers and web requests

### Sample 1 (js)

~~~~js
import http from 'node:http'

const notes = [{ id: 1, title: 'Learn request methods' }]

function sendJson(response, statusCode, value) {
  response.writeHead(statusCode, {
    'content-type': 'application/json; charset=utf-8'
  })
  response.end(JSON.stringify(value))
}

const server = http.createServer(async (request, response) => {
  const requestUrl = new URL(request.url || '/', 'http://localhost')

  if (request.method === 'GET' && requestUrl.pathname === '/health') {
    sendJson(response, 200, { status: 'ok' })
    return
  }

  if (request.method === 'GET' && requestUrl.pathname === '/notes') {
    sendJson(response, 200, notes)
    return
  }

  sendJson(response, 404, { error: 'Route not found' })
})

server.listen(3000, '127.0.0.1', () => {
  console.log('Server listening at http://127.0.0.1:3000')
})
~~~~

### Sample 2 (js)

~~~~js
function readJson(request, limit = 1024 * 1024) {
  return new Promise((resolve, reject) => {
    const chunks = []
    let size = 0
    let tooLarge = false

    request.on('data', (chunk) => {
      size += chunk.length
      if (size > limit) {
        tooLarge = true
        chunks.length = 0
        return
      }
      if (!tooLarge) chunks.push(chunk)
    })

    request.on('error', reject)

    request.on('end', () => {
      if (tooLarge) {
        const error = new Error('Request body is too large')
        error.statusCode = 413
        reject(error)
        return
      }

      try {
        resolve(JSON.parse(Buffer.concat(chunks).toString('utf8')))
      } catch {
        const error = new Error('Request body must be valid JSON')
        error.statusCode = 400
        reject(error)
      }
    })
  })
}
~~~~

### Sample 3 (js)

~~~~js
if (request.method === 'POST' && requestUrl.pathname === '/notes') {
  const contentType = request.headers['content-type'] || ''
  if (!contentType.startsWith('application/json')) {
    sendJson(response, 415, { error: 'Send application/json' })
    return
  }

  try {
    const body = await readJson(request)
    if (typeof body.title !== 'string' || !body.title.trim()) {
      sendJson(response, 400, { error: 'title is required' })
      return
    }

    const note = { id: Date.now(), title: body.title.trim() }
    notes.push(note)
    sendJson(response, 201, note)
  } catch (error) {
    sendJson(response, error.statusCode || 500, {
      error: error.statusCode ? error.message : 'Request failed'
    })
  }
  return
}
~~~~

### Sample 4 (js)

~~~~js
const response = await fetch('http://127.0.0.1:3000/notes')

if (!response.ok) {
  throw new Error('Request failed with status ' + response.status)
}

const notes = await response.json()
console.log(notes)
~~~~

## 11. Errors, process, and signals

### Sample 1 (js)

~~~~js
try {
  const value = JSON.parse('not json')
  console.log(value)
} catch (error) {
  console.error('Could not parse input:', error.message)
}
~~~~

### Sample 2 (js)

~~~~js
try {
  const text = await readFile('./missing.txt', 'utf8')
  console.log(text)
} catch (error) {
  console.error('Could not load file:', error.message)
}
~~~~

### Sample 3 (js)

~~~~js
async function loadSettings(filePath) {
  try {
    return JSON.parse(await readFile(filePath, 'utf8'))
  } catch (cause) {
    throw new Error('Could not load settings', { cause })
  }
}
~~~~

### Sample 4 (js)

~~~~js
try {
  const result = await saveRecord(input)
  sendJson(response, 201, result)
} catch (error) {
  console.error('Saving record failed:', error)
  sendJson(response, 500, { error: 'Could not save record' })
}
~~~~

### Sample 5 (js)

~~~~js
try {
  await run()
} catch (error) {
  console.error(error.message)
  process.exitCode = 1
}
~~~~

### Sample 6 (js)

~~~~js
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
~~~~

## 12. Environment configuration and diagnostics

### Sample 1 (js)

~~~~js
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
~~~~

### Sample 2 (sh)

~~~~sh
node --env-file=.env app.js
~~~~

### Sample 3 (dotenv)

~~~~dotenv
PORT=3000
LOG_LEVEL=debug
DATABASE_URL=replace-with-a-local-value
~~~~

### Sample 4 (js)

~~~~js
console.info('note_saved', { noteId: 42 })
console.error('database_connection_failed', {
  message: error.message,
  name: error.name
})
~~~~

### Sample 5 (js)

~~~~js
import { performance } from 'node:perf_hooks'

const startedAt = performance.now()
await runTask()
const elapsedMs = performance.now() - startedAt

console.log('task_finished', { elapsedMs })
console.log('memory', process.memoryUsage())
~~~~

### Sample 6 (sh)

~~~~sh
node --inspect app.js
~~~~

## 13. Child processes and worker threads

### Sample 1 (js)

~~~~js
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
~~~~

### Sample 2 (js)

~~~~js
import { parentPort, workerData } from 'node:worker_threads'

let total = 0
for (let value = 1; value <= workerData.limit; value += 1) {
  total += value
}

parentPort.postMessage(total)
~~~~

### Sample 3 (js)

~~~~js
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
~~~~

## 14. Testing with the built-in test runner

### Sample 1 (js)

~~~~js
export function addTax(amount, rate) {
  if (!Number.isFinite(amount) || amount < 0) {
    throw new TypeError('amount must be a non-negative number')
  }

  if (!Number.isFinite(rate) || rate < 0 || rate > 1) {
    throw new RangeError('rate must be between 0 and 1')
  }

  return amount + amount * rate
}
~~~~

### Sample 2 (js)

~~~~js
import test from 'node:test'
import assert from 'node:assert/strict'
import { addTax } from '../price.js'

test('adds tax to a valid amount', () => {
  assert.equal(addTax(100, 0.1), 110)
})

test('rejects a negative amount', () => {
  assert.throws(() => addTax(-1, 0.1), {
    name: 'TypeError'
  })
})

test('rejects a rate above one', () => {
  assert.throws(() => addTax(100, 1.1), RangeError)
})
~~~~

### Sample 3 (sh)

~~~~sh
node --test
~~~~

### Sample 4 (js)

~~~~js
test('reports a missing file', async () => {
  await assert.rejects(
    readFile('./does-not-exist.txt', 'utf8'),
    { code: 'ENOENT' }
  )
})
~~~~

### Sample 5 (json)

~~~~json
{
  "scripts": {
    "test": "node --test"
  }
}
~~~~

## 15. Security, performance, and production practices

## 16. Build and test a small Node.js service

### Sample 1 (js)

~~~~js
import http from 'node:http'
import { randomUUID } from 'node:crypto'
import { pathToFileURL } from 'node:url'

function sendJson(response, statusCode, value, headers = {}) {
  response.writeHead(statusCode, {
    'content-type': 'application/json; charset=utf-8',
    ...headers
  })
  response.end(JSON.stringify(value))
}

function readJson(request, limit = 64 * 1024) {
  return new Promise((resolve, reject) => {
    const chunks = []
    let size = 0
    let tooLarge = false

    request.on('data', (chunk) => {
      size += chunk.length
      if (size > limit) {
        tooLarge = true
        chunks.length = 0
        return
      }
      if (!tooLarge) chunks.push(chunk)
    })

    request.on('error', reject)
    request.on('aborted', () => reject(new Error('Request was aborted')))

    request.on('end', () => {
      if (tooLarge) {
        const error = new Error('Request body is too large')
        error.statusCode = 413
        reject(error)
        return
      }

      try {
        resolve(JSON.parse(Buffer.concat(chunks).toString('utf8')))
      } catch {
        const error = new Error('Request body must be valid JSON')
        error.statusCode = 400
        reject(error)
      }
    })
  })
}

export function createAppServer() {
  const notes = []

  return http.createServer(async (request, response) => {
    const requestUrl = new URL(request.url || '/', 'http://localhost')

    if (request.method === 'GET' && requestUrl.pathname === '/health') {
      sendJson(response, 200, { status: 'ok' })
      return
    }

    if (requestUrl.pathname === '/notes' && request.method === 'GET') {
      sendJson(response, 200, notes)
      return
    }

    if (requestUrl.pathname === '/notes' && request.method === 'POST') {
      const contentType = request.headers['content-type'] || ''
      if (!contentType.startsWith('application/json')) {
        sendJson(response, 415, { error: 'Send application/json' })
        return
      }

      try {
        const body = await readJson(request)
        if (
          !body ||
          typeof body !== 'object' ||
          Array.isArray(body) ||
          typeof body.title !== 'string' ||
          !body.title.trim() ||
          body.title.length > 120
        ) {
          sendJson(response, 400, {
            error: 'title must be a non-empty string of at most 120 characters'
          })
          return
        }

        const note = {
          id: randomUUID(),
          title: body.title.trim(),
          createdAt: new Date().toISOString()
        }
        notes.push(note)
        sendJson(response, 201, note)
      } catch (error) {
        const statusCode = error.statusCode || 500
        sendJson(response, statusCode, {
          error: statusCode < 500 ? error.message : 'Request failed'
        })
      }
      return
    }

    if (requestUrl.pathname === '/notes') {
      sendJson(response, 405, { error: 'Method not allowed' }, {
        allow: 'GET, POST'
      })
      return
    }

    sendJson(response, 404, { error: 'Route not found' })
  })
}

const entryUrl = process.argv[1]
  ? pathToFileURL(process.argv[1]).href
  : ''

if (entryUrl === import.meta.url) {
  const port = Number(process.env.PORT || 3000)
  if (!Number.isInteger(port) || port < 1 || port > 65535) {
    throw new Error('PORT must be an integer from 1 to 65535')
  }

  const server = createAppServer()
  server.listen(port, '127.0.0.1', () => {
    console.log('Notes service listening on port', port)
  })

  let shuttingDown = false
  function shutdown(signal) {
    if (shuttingDown) return
    shuttingDown = true
    console.log('Received', signal, 'starting shutdown')
    server.close((error) => {
      if (error) {
        console.error('Shutdown failed:', error.message)
        process.exitCode = 1
      }
    })
  }

  process.on('SIGINT', () => shutdown('SIGINT'))
  process.on('SIGTERM', () => shutdown('SIGTERM'))
}
~~~~

### Sample 2 (sh)

~~~~sh
curl http://127.0.0.1:3000/health
curl http://127.0.0.1:3000/notes
~~~~

### Sample 3 (sh)

~~~~sh
curl -X POST http://127.0.0.1:3000/notes -H "content-type: application/json" -d "{\"title\":\"Review streams\"}"
~~~~

### Sample 4 (js)

~~~~js
import test from 'node:test'
import assert from 'node:assert/strict'
import { once } from 'node:events'
import { createAppServer } from '../app.js'

test('creates a note and returns it in the list', async (context) => {
  const server = createAppServer()
  server.listen(0, '127.0.0.1')
  await once(server, 'listening')

  context.after(() => new Promise((resolve, reject) => {
    server.close((error) => error ? reject(error) : resolve())
  }))

  const address = server.address()
  const baseUrl = 'http://127.0.0.1:' + address.port

  const createdResponse = await fetch(baseUrl + '/notes', {
    method: 'POST',
    headers: { 'content-type': 'application/json' },
    body: JSON.stringify({ title: 'Review streams' })
  })

  assert.equal(createdResponse.status, 201)
  const created = await createdResponse.json()
  assert.equal(created.title, 'Review streams')
  assert.ok(created.id)

  const listResponse = await fetch(baseUrl + '/notes')
  assert.equal(listResponse.status, 200)
  assert.deepEqual(await listResponse.json(), [created])
})

test('rejects an empty note title', async (context) => {
  const server = createAppServer()
  server.listen(0, '127.0.0.1')
  await once(server, 'listening')

  context.after(() => new Promise((resolve, reject) => {
    server.close((error) => error ? reject(error) : resolve())
  }))

  const address = server.address()
  for (const body of [{ title: '   ' }, null, []]) {
    const response = await fetch('http://127.0.0.1:' + address.port + '/notes', {
      method: 'POST',
      headers: { 'content-type': 'application/json' },
      body: JSON.stringify(body)
    })

    assert.equal(response.status, 400)
  }
})
~~~~

