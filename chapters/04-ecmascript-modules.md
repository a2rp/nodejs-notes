# 4. ECMAScript modules

[Back to notes index](../README.md)

| [Previous: CommonJS modules and project structure](./03-commonjs-modules-and-project-structure.md) | [Notes index](../README.md) | [Next: npm, package.json, and dependencies](./05-npm-package-json-and-dependencies.md) |
| --- | --- | --- |

## What ESM changes

ECMAScript modules, usually shortened to ESM, are JavaScript’s standard module syntax. They use **import** and **export** declarations. Node.js can treat JavaScript files as ESM when the nearest **package.json** declares **"type": "module"**, or when the filename ends in **.mjs**.

A package can start with this minimal metadata:

~~~json
{
  "name": "price-check",
  "private": true,
  "type": "module"
}
~~~

In this package, **.js** files are interpreted as ESM. A **.cjs** file remains CommonJS. The **.mjs** extension explicitly selects ESM even outside a package configured this way.

## Export and import named values

Create **price.js**:

~~~js
export function addTax(amount, rate) {
  if (!Number.isFinite(amount) || amount < 0) {
    throw new TypeError('amount must be a non-negative number')
  }

  if (!Number.isFinite(rate) || rate < 0 || rate > 1) {
    throw new RangeError('rate must be between 0 and 1')
  }

  return amount + amount * rate
}
~~~

Import it from **app.js**:

~~~js
import { addTax } from './price.js'

console.log(addTax(100, 0.1))
~~~

Relative ESM imports should include the file extension. This makes the target file explicit and avoids assumptions about extension lookup.

## Default exports

A module can also export one primary value:

~~~js
export default function formatPrice(amount) {
  return amount.toFixed(2)
}
~~~

The caller chooses the local name:

~~~js
import formatPrice from './format-price.js'

console.log(formatPrice(12.5))
~~~

Use named exports when a module exposes a set of related operations. Use a default export when the module has one clear primary value. Keep the choice consistent within a project.

## Import built-in modules

Node.js built-in modules can be imported with the **node:** prefix:

~~~js
import { readFile } from 'node:fs/promises'
import path from 'node:path'

const filePath = path.resolve('notes.txt')
const text = await readFile(filePath, 'utf8')
console.log(text)
~~~

At the top level of an ESM file, **await** can be used directly. A rejected operation still needs deliberate error handling, such as a surrounding **try/catch** or a top-level handler in the application entry point.

## Find a file beside the current module

ESM files do not provide the CommonJS **__dirname** variable. Use **import.meta.url** with the URL helpers when a path should be based on the current module:

~~~js
import { readFile } from 'node:fs/promises'
import { fileURLToPath } from 'node:url'

const currentFile = fileURLToPath(import.meta.url)
const currentDirectory = fileURLToPath(new URL('.', import.meta.url))
const text = await readFile(new URL('./notes.txt', import.meta.url), 'utf8')

console.log(currentFile)
console.log(currentDirectory)
console.log(text)
~~~

For a resource next to the module, passing a URL directly to filesystem APIs often avoids manual path conversion.

## Load a module dynamically

Static imports are resolved before the module body runs. Use **import()** when a module should be loaded conditionally or on demand:

~~~js
const selectedModule = './price.js'

const { addTax } = await import(selectedModule)
console.log(addTax(50, 0.08))
~~~

Dynamic import returns a promise. Handle a failure if the module path depends on user input or runtime configuration.

## Keep module boundaries clear

A package’s module mode is determined by the nearest package boundary. A nested folder with its own **package.json** can define a different mode. Make module type obvious in repository metadata, and avoid mixing extensions without a reason.

## Practice

Convert the Chapter 3 example into ESM. Add **"type": "module"** to **package.json**, change the import and export syntax, and include the **.js** extension in the relative import. Run the entry point and compare the result.

## Check what you learned

1. Which JavaScript keywords are used to import and export ESM values?
2. What does **"type": "module"** change for files ending in **.js**?
3. Which extensions explicitly select ESM and CommonJS?
4. Why should a relative ESM import include its file extension?
5. How does a named import differ from a default import?
6. What does **import.meta.url** represent?
7. When is dynamic **import()** useful?
8. How can a package make its module mode clear?

## References

- [ECMAScript modules](https://nodejs.org/api/esm.html)
- [Modules: packages](https://nodejs.org/api/packages.html)
- [Node.js URL API](https://nodejs.org/api/url.html)