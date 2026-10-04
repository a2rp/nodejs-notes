# 7. Events and EventEmitter

[Back to notes index](../README.md)

| [Previous: Asynchronous JavaScript and the event loop](./06-async-javascript-and-event-loop.md) | [Notes index](../README.md) | [Next: Files, paths, and URLs](./08-files-paths-and-urls.md) |
| --- | --- | --- |

## What an event represents

An event reports that something happened. One part of a program emits a named event, and other parts that registered listeners can react to it. This helps separate the code that detects an action from the code that responds.

Node.js provides **EventEmitter** in the **node:events** module:

~~~js
const { EventEmitter } = require('node:events')

const orderEvents = new EventEmitter()

orderEvents.on('created', (order) => {
  console.log('New order:', order.id)
})

orderEvents.emit('created', { id: 'order-104', total: 25 })
~~~

Listeners receive the values passed to **emit**. In this example, the listener receives the order object.

## Register listeners

Use **on** when a listener should receive every event of that name. Use **once** when it should run only for the next matching event:

~~~js
orderEvents.once('ready', () => {
  console.log('The first ready event was received')
})

orderEvents.emit('ready')
orderEvents.emit('ready')
~~~

The callback runs only once. Event names are strings, so keep them consistent. A shared constants object can help larger projects avoid spelling differences.

## Listener execution and cleanup

EventEmitter calls listeners synchronously, in the order they were registered. If a listener does expensive work, it delays the code that called **emit**.

A listener can be removed with **off** when it is no longer needed:

~~~js
function logOrder(order) {
  console.log('Order:', order.id)
}

orderEvents.on('created', logOrder)
orderEvents.emit('created', { id: 'order-105' })
orderEvents.off('created', logOrder)
~~~

The same function reference must be passed to **off**. An inline anonymous function cannot be removed later unless its reference was saved.

Avoid repeatedly adding listeners to a long-lived emitter without removing them when their work is finished. Accumulating listeners can cause duplicate behavior and warnings.

## Handle the error event

The **error** event is special. If an EventEmitter emits **error** and there is no listener for it, Node.js throws the error. Register a listener when the emitter may report errors:

~~~js
orderEvents.on('error', (error) => {
  console.error('Order event failed:', error.message)
})

orderEvents.emit('error', new Error('Payment was declined'))
~~~

An error listener should record or route the failure appropriately. Do not silently swallow an error that means the application could not complete its work.

## Keep event-driven code understandable

Events are useful when several independent parts need to react to a meaningful change. For a single direct operation, a normal function call is usually easier to trace.

An EventEmitter is local to one Node.js process. It does not send events to another server, persist them, or provide a message queue. For communication across processes, use an appropriate network or messaging system.

If a listener is declared **async**, EventEmitter does not wait for its returned Promise before continuing to other listeners. Handle rejected work inside that listener or use a Promise-based coordination pattern when completion order matters.

## Practice

Create an emitter for a file-processing job. Emit **started**, **finished**, and **error** with small objects. Add a one-time listener for **finished**, emit twice, and verify it only reacts once. Then remove a named listener and emit again.

## Check what you learned

1. What is an event useful for communicating?
2. Which built-in module provides EventEmitter?
3. What is the difference between **on** and **once**?
4. In what order are listeners called?
5. Why should a listener avoid expensive synchronous work?
6. What happens when **error** is emitted without an error listener?
7. Why must **off** receive the same function reference that was registered?
8. Does an EventEmitter send events to another Node.js process?

## References

- [Events API](https://nodejs.org/api/events.html)
- [Node.js errors](https://nodejs.org/api/errors.html)