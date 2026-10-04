# 9. Buffers and streams

[Back to notes index](../README.md)

| [Previous: Files, paths, and URLs](./08-files-paths-and-urls.md) | [Notes index](../README.md) | [Next: HTTP servers and web requests](./10-http-servers-and-web-requests.md) |
| --- | --- | --- |

## What a Buffer stores

A **Buffer** is a fixed-size sequence of bytes. It is useful when working with file data, network data, or binary formats. Text is encoded into bytes, so the number of characters and the number of bytes may differ.

~~~js
const greeting = Buffer.from('hello', 'utf8')

console.log(greeting)
console.log(greeting.toString('utf8'))
console.log(Buffer.byteLength('hello', 'utf8'))
~~~

Use the correct text encoding when converting bytes to a string. A buffer is not itself a JavaScript string.

## Why use streams

Reading a large file into one string or buffer can consume substantial memory. A stream lets a program process data in chunks as it arrives.

Common stream types include:

- Readable streams provide data.
- Writable streams accept data.
- Duplex streams can read and write.
- Transform streams change data while it flows through.

Node.js file APIs expose readable and writable streams. HTTP requests and responses are streams as well.

## Copy a file with pipeline

**pipeline** connects streams, manages backpressure, and forwards errors. The Promise-based form lets the caller await completion:

~~~js
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
~~~

The example finishes after the data reaches the destination or a stream reports an error. Ensure the destination folder exists before opening its file.

## Transform data while it flows

A Transform stream is both readable and writable. The following example converts an ASCII text file to uppercase while copying it:

~~~js
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
~~~

This simple transform converts each chunk independently and is suitable for ASCII text. Character encodings such as UTF-8 can use multiple bytes per character, so production text processing should preserve character boundaries or use a decoder designed for streaming.

## Understand backpressure

A fast readable source may produce data faster than a writable destination can consume it. Backpressure gives the destination a way to slow the source so memory does not grow without bound.

When manually writing to a stream, **write()** can return **false**. Stop sending more data until the writable stream emits **drain**. Prefer **pipeline** for ordinary stream chains because it coordinates this flow and propagates errors.

~~~js
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
~~~

A production helper must also handle stream errors while waiting. This snippet is only to demonstrate the writable backpressure signal; use **pipeline** for a complete flow.

## Practice

Copy a large text file with a stream pipeline. Compare the memory behavior with **readFile** followed by **writeFile**. Then insert a Transform that counts bytes or changes simple ASCII text, and handle a missing input file.

## Check what you learned

1. What kind of data does a Buffer hold?
2. Why can a string's character count differ from its byte length?
3. What does a readable stream provide?
4. What does a Transform stream do?
5. Why can streams use less memory for large files?
6. What problem does backpressure help control?
7. What does a false return from **write()** mean?
8. Why is **pipeline** useful when connecting streams?

## References

- [Buffer API](https://nodejs.org/api/buffer.html)
- [Stream API](https://nodejs.org/api/stream.html)
- [Stream promises](https://nodejs.org/api/stream.html#streams-promises-api)
- [File system streams](https://nodejs.org/api/fs.html#class-fsreadstream)