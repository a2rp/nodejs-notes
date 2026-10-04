# 10. HTTP servers and web requests

[Back to notes index](../README.md)

| [Previous: Buffers and streams](./09-buffers-and-streams.md) | [Notes index](../README.md) | [Next: Errors, process, and signals](./11-errors-process-and-signals.md) |
| --- | --- | --- |

## Create a small HTTP server

Node.js includes the **node:http** module. A server receives a request and writes a response. The request contains a method, URL, headers, and possibly a body. A response needs a status code and headers that describe its content.

Create **server.js** in an ESM package:

~~~js
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
~~~

Start it with **node server.js**. Visit **http://127.0.0.1:3000/health** or request **/notes**. Binding to the loopback address keeps this practice server local to the machine.

## Route by method and path

A URL path alone is not a complete route. **GET /notes** and **POST /notes** have different purposes. Check both **request.method** and the parsed URL path.

Always send an explicit response for a matched route. If no route matches, return **404**. If the method is known but unsupported for a path, **405 Method Not Allowed** can be more precise and should include an **Allow** header listing accepted methods.

Use the **URL** class to read a path or query parameter. Do not compare the full request URL as a raw string because query ordering and encoding can vary.

## Read and validate a JSON request body

A request body is a stream. Collect it with a size limit, check its content type, parse JSON, and validate the resulting value before storing or using it:

~~~js
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
~~~

The request is still consumed after the limit is exceeded, but additional chunks are not retained in memory. A production endpoint should also set appropriate timeouts and decide which content types it accepts.

Use the helper inside an asynchronous route:

~~~js
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
~~~

This route belongs inside an **async** request handler. In a real service, use a persistent store, stable identifiers, and stricter request validation.

## Call an HTTP endpoint with fetch

Node.js provides **fetch** for making web requests in current supported releases:

~~~js
const response = await fetch('http://127.0.0.1:3000/notes')

if (!response.ok) {
  throw new Error('Request failed with status ' + response.status)
}

const notes = await response.json()
console.log(notes)
~~~

A completed HTTP response does not automatically mean the request succeeded. Check **response.ok** or the status code before treating the body as a successful result.

## Return the right response

Use status codes to describe the outcome:

- **200** means the request succeeded.
- **201** means a resource was created.
- **400** means the request data is invalid.
- **404** means the path has no matching resource.
- **413** means the request body is too large.
- **415** means the request content type is unsupported.
- **500** means the server failed unexpectedly.

Do not send internal stack traces or secret configuration values to an HTTP client. Log useful details on the server and return a safe error message.

## Practice

Add a **POST /notes** route to the server. Accept a JSON object with a non-empty **title**, return **201**, and make **GET /notes** show the new entry. Try an invalid JSON body, a missing title, an oversized body, and an unknown route.

## Check what you learned

1. Which built-in module creates an HTTP server?
2. What details should a route check besides the URL path?
3. Why should request URLs be parsed with the URL class?
4. Why must a JSON request body have a size limit?
5. What should the server validate after parsing JSON?
6. Which status code describes a newly created resource?
7. Why should an HTTP client inspect **response.ok**?
8. What information should a server avoid returning in an error response?

## References

- [HTTP API](https://nodejs.org/api/http.html)
- [Fetch API](https://nodejs.org/api/globals.html#fetch)
- [URL API](https://nodejs.org/api/url.html)
- [Node.js security best practices](https://nodejs.org/en/learn/getting-started/security-best-practices)