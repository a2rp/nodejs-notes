# 3. CommonJS modules and project structure

[Back to notes index](../README.md)

| [Previous: Install Node.js and use the command line](./02-install-nodejs-and-command-line.md) | [Notes index](../README.md) | [Next: ECMAScript modules](./04-ecmascript-modules.md) |
| --- | --- | --- |

## Why split code into modules

A module keeps a related piece of code in its own file. A small project might separate its entry point, reusable functions, and data access code:

~~~text
price-check/
  app.cjs
  price.cjs
~~~

CommonJS is one module system supported by Node.js. In a **.cjs** file, use **require()** to load a module and **module.exports** to choose the value other files receive.

## Export a function

Create **price.cjs**:

~~~js
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
~~~

Create **app.cjs** beside it:

~~~js
const { addTax } = require('./price.cjs')

console.log(addTax(100, 0.1))
~~~

Run it with **node app.cjs**. The relative path starts with **./** because the file is part of this project. A package name such as **node:path** refers to a built-in module, while a name such as **some-package** is resolved as an installed package.

## Understand exports

Each CommonJS file has a **module** object. **module.exports** is the value that **require()** returns.

~~~js
module.exports = function add(a, b) {
  return a + b
}
~~~

A caller can then use the function directly:

~~~js
const add = require('./add.cjs')
console.log(add(2, 3))
~~~

The local **exports** name initially refers to the same object as **module.exports**, so adding a property works:

~~~js
exports.add = (a, b) => a + b
~~~

Reassigning **exports** alone does not replace the exported value. When an entire function or another value should be exported, assign it to **module.exports**.

## Use built-in modules explicitly

The **node:** prefix makes it clear that a dependency is part of Node.js:

~~~js
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
~~~

This example uses the Promise-based filesystem API. Error handling is attached to the returned promise so a failed read is not left unhandled.

## Module loading and caching

When a CommonJS file is loaded, Node.js evaluates it and caches the exported value for that resolved file. Requiring the same resolved file again normally returns the cached module rather than running its top-level code again.

Keep reusable work inside exported functions instead of relying on top-level side effects. This makes modules easier to test and avoids surprising behavior during imports.

Circular dependencies occur when two modules require each other. During that cycle, one module may observe another module before its exports are fully assigned. Prefer a clear one-directional dependency structure where possible.

## Practice

Move **addTax** into its own **price.cjs** file, export it, and call it from **app.cjs**. Try invalid values and confirm that the caller receives an error. Add a second exported function and import only the function the entry point needs.

## Check what you learned

1. What problem does splitting code into modules solve?
2. Which function loads a CommonJS module?
3. What value does **require()** return?
4. How can a module export several named functions?
5. Why can assigning a new value to **exports** be misleading?
6. What does the **node:** prefix identify?
7. Why does Node.js cache loaded CommonJS modules?
8. What can make circular dependencies difficult to reason about?

## References

- [CommonJS modules](https://nodejs.org/api/modules.html)
- [Modules: packages](https://nodejs.org/api/packages.html)
- [Node.js built-in modules](https://nodejs.org/api/modules.html#built-in-modules)