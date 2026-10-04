# 16. Build and test a small Node.js service

[Back to notes index](../README.md)

| [Previous: Security, performance, and production practices](./15-security-performance-and-production.md) | [Notes index](../README.md) | [Next: All code samples](./98-all-code-samples.md) |
| --- | --- | --- |

## What we will build

This small service stores notes in memory and exposes three routes:

- **GET /health** returns a health response.
- **GET /notes** returns the current notes.
- **POST /notes** validates and creates one note.

The data disappears when the process stops. This example is for learning HTTP boundaries and tests, not persistent storage.

## Create the server

Create **app.js** in an ESM package:

~~~js
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
~~~

Start the service with **node app.js**. It listens on the local machine at port **3000** unless **PORT** is set.

## Try the routes

Check health and list notes:

~~~sh
curl http://127.0.0.1:3000/health
curl http://127.0.0.1:3000/notes
~~~

Create a note:

~~~sh
curl -X POST http://127.0.0.1:3000/notes -H "content-type: application/json" -d "{\"title\":\"Review streams\"}"
~~~

The service returns **201 Created** for a valid note. Try an empty title, malformed JSON, a body larger than the limit, a request without the JSON content type, and an unknown path. Observe the status code and response body.

## Add an automated test

Create **test/app.test.js**:

~~~js
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
~~~

Run it with **node --test**. The server binds to port zero in the test, so the operating system selects an available port. The test closes the server after its assertions.

## Review the complete flow

The route checks the method, path, and content type before reading a body. It limits retained request data, parses JSON, validates the title, and returns an appropriate status. The test exercises the public HTTP behavior rather than inspecting private implementation details.

Before adapting this pattern for real use, replace the in-memory array with persistent storage, add authentication and authorization where needed, choose deployment network binding deliberately, and apply request timeouts and operational monitoring.

## Check what you learned

1. Which routes does the service expose?
2. Why does **createAppServer** return a server without starting it?
3. How does the body reader limit retained data?
4. Which status code is returned when a note is created?
5. What does the **Allow** header communicate for **405**?
6. Why does the test listen on port zero?
7. Why does the test close its server after finishing?
8. Which parts must change before using this in-memory example for durable production data?

## References

- [HTTP API](https://nodejs.org/api/http.html)
- [Fetch API](https://nodejs.org/api/globals.html#fetch)
- [Test runner](https://nodejs.org/api/test.html)
- [Node.js security best practices](https://nodejs.org/en/learn/getting-started/security-best-practices)
- [Process API](https://nodejs.org/api/process.html)