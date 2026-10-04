# 14. Testing with the built-in test runner

[Back to notes index](../README.md)

| [Previous: Child processes and worker threads](./13-child-processes-and-worker-threads.md) | [Notes index](../README.md) | [Next: Security, performance, and production practices](./15-security-performance-and-production.md) |
| --- | --- | --- |

## Why write automated tests

A test runs code with known inputs and checks the result. It helps catch regressions when a function changes and makes expected behavior visible to the next person reading the project.

Node.js includes a test runner in **node:test** and assertions in **node:assert**. A separate test framework is not required for basic unit tests.

## Test a small function

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

Create **test/price.test.js**:

~~~js
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
~~~

Run all discovered tests with:

~~~sh
node --test
~~~

A passing test exits successfully. A failed assertion or thrown error is reported and causes a nonzero exit code.

## Assert asynchronous behavior

An asynchronous test can return or await a Promise. Use **assert.rejects** to verify a rejected operation:

~~~js
test('reports a missing file', async () => {
  await assert.rejects(
    readFile('./does-not-exist.txt', 'utf8'),
    { code: 'ENOENT' }
  )
})
~~~

Make the test itself **async** and await the assertion. Otherwise, the runner may finish before the assertion settles.

## Test behavior at clear boundaries

Prefer small tests that verify public behavior. Include normal input, boundary values, and invalid input. A test should fail for the bug it is meant to detect.

Keep unit tests independent from external services where practical. For HTTP handlers, use a local server and make actual requests when the route behavior is what needs testing. Close servers and other resources so the test process can exit.

Do not assert on private implementation details if a result or observable effect can be checked instead.

## Add an npm test script

In **package.json**, define:

~~~json
{
  "scripts": {
    "test": "node --test"
  }
}
~~~

Run **npm test** from the package folder. This makes the project's test command easy to remember and usable by automated checks.

## Practice

Add tests for valid price calculations, negative values, an invalid tax rate, and a rounding edge case. Deliberately change the expected result and observe the failure output, then restore the correct assertion.

## Check what you learned

1. Which built-in module provides the test runner?
2. Which built-in module provides strict assertions?
3. What does **node --test** do?
4. Which assertion checks that synchronous code throws?
5. Which assertion checks that a Promise rejects?
6. Why should an async test await its assertions?
7. What kinds of cases should a small function test cover?
8. Why should a test close the server or other resources it opens?

## References

- [Node.js test runner](https://nodejs.org/api/test.html)
- [Assertion testing](https://nodejs.org/api/assert.html)
- [Node.js CLI test options](https://nodejs.org/api/cli.html#test-runner)