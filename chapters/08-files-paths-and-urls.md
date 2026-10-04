# 8. Files, paths, and URLs

[Back to notes index](../README.md)

| [Previous: Events and EventEmitter](./07-events-and-eventemitter.md) | [Notes index](../README.md) | [Next: Buffers and streams](./09-buffers-and-streams.md) |
| --- | --- | --- |

## Read and write files with promises

Node.js includes **node:fs/promises** for asynchronous filesystem operations. A small script can create a folder, write a file, and read it back:

~~~js
import { mkdir, readFile, writeFile } from 'node:fs/promises'
import path from 'node:path'

const dataDirectory = path.resolve('data')
const filePath = path.join(dataDirectory, 'note.txt')

await mkdir(dataDirectory, { recursive: true })
await writeFile(filePath, 'Practice file operations\n', 'utf8')

const text = await readFile(filePath, 'utf8')
console.log(text)
~~~

Filesystem operations can fail because a file is missing, permission is denied, or a device has a problem. Catch errors at a point where the program can add useful context or choose a safe response.

Avoid synchronous file operations in a server request path. They block JavaScript while the operation completes.

## Build paths with node:path

Do not combine path fragments with a slash string. Separators vary by operating system. Use **node:path**:

~~~js
import path from 'node:path'

const filePath = path.join('data', 'notes', 'today.txt')
console.log(filePath)
~~~

**path.join** combines path pieces. **path.resolve** produces an absolute path from a starting folder and the supplied pieces. The current working directory affects relative calls to **path.resolve**.

Use the path API for filesystem paths. Use the URL API for web addresses and URL query strings. They solve different problems.

## Keep user paths inside an allowed directory

Never assume a path received from a request or command-line argument is safe. A value such as **../../private.txt** can escape a data folder if it is joined without a boundary check.

~~~js
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
~~~

Use a boundary check after resolving the path. A simple string prefix check is unsafe because a sibling folder may begin with the same characters as the intended directory. Also consider symbolic links if untrusted users can create files inside the allowed directory.

## Parse a URL and its query values

Use the **URL** class rather than splitting a URL string manually:

~~~js
const requestUrl = new URL(
  '/search?topic=streams&page=2',
  'http://localhost:3000'
)

console.log(requestUrl.pathname)
console.log(requestUrl.searchParams.get('topic'))
console.log(requestUrl.searchParams.get('page'))
~~~

The base is required when parsing a relative URL. Validate query values before using them, because values from the URL are strings and can be missing or malformed.

## Work with file URLs in ESM

ESM identifies its current file using **import.meta.url**, which is a file URL. Convert it when a filesystem path is needed:

~~~js
import { fileURLToPath } from 'node:url'
import path from 'node:path'

const currentFile = fileURLToPath(import.meta.url)
const currentDirectory = path.dirname(currentFile)

console.log(currentDirectory)
~~~

When opening a resource beside the current module, a file URL can be used directly:

~~~js
import { readFile } from 'node:fs/promises'

const text = await readFile(new URL('./message.txt', import.meta.url), 'utf8')
console.log(text)
~~~

## Practice

Create a **data** folder, write a text file, and read it back. Parse a URL with two query values. Pass both a normal relative path and a traversal path to **resolveInside**, and confirm only the safe path is accepted.

## Check what you learned

1. Which module exposes promise-based file operations?
2. Why are asynchronous file APIs preferred in server request handlers?
3. When should **path.join** or **path.resolve** be used?
4. How is a filesystem path different from a web URL?
5. Why is a string prefix check insufficient for path containment?
6. Which URL property gives access to query parameters?
7. How can ESM code convert its file URL to a filesystem path?
8. What extra risk can symbolic links create for a directory boundary check?

## References

- [File system API](https://nodejs.org/api/fs.html)
- [Path API](https://nodejs.org/api/path.html)
- [URL API](https://nodejs.org/api/url.html)
- [URL and file paths](https://nodejs.org/api/url.html#urlfileurltopathurl-options)