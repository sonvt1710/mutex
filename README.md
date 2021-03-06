# Mutex

Asynchronous, synchronous and delayed Mutex with ES6 and Promises.


## Table of contents

* [Setup](#setup)
* [Usage](#usage)
* [API](#api)
  * [Properties](#properties) 
  * [Methods](#methods) 


## Setup

This module can then be installed with yarn:
```shell
yarn add @jdes/mutex
```

## Usage

Import module:

```javascript
/**
 * @class {Mutex}
 */
const Mutex = require('@jdes/mutex');
```

Instantiate:

```javascript
/**
 * @type {Mutex}
 */
const mutex = new Mutex();
```

Example:

```javascript
// Instantiate
const mutex = new Mutex();
let name = null;
let iterations = 0;

// Lock and perform and unlock asynchronously
mutex.asynchronous()
  .then((unlock) => {
    name = 'Jean Desravines';
    unlock();
  })
  .catch(() => {
    console.error('Mutex is locked');
  });

// Lock, perform and unlock asynchronously with delay.
// This section try to lock every 100ms but the delay accept to be locked every 200ms.
const interval = setInterval(() => {
  mutex.delayed(200)
    .then(() => iterations++)
    .then(() => {
      if (iterations === 5) {
        clearInterval(interval);
      } else {
        console.log(iterations);
      }
    });
}, 100);
```


## API

### Properties

#### locked: boolean

Indicate if the mutex is locked

Example:

```javascript
console.log(mutex.locked ? 'Locked' : 'Unlocked');
```

### Methods

#### lock(): boolean

* returns true if the mutex success to lock

Try to lock synchronously the mutex and return true if it success

Example:

```javascript
if (mutex.lock()) {
  console.log('Success');
} else {
  console.error('Error');
}
```

#### unlock(): Promise

* returns a resolved Promise

Unlock the mutex

Example:

```javascript
mutex.unlock()
  .then(() => {
    console.log('Unlocked');
  });
```

#### asynchronous(): Promise

* return a Promise resolved with the unlock callback if the mutex success to lock.

Try to lock the mutex and resolve the promise

Example:

```javascript
mutex.asynchronous()
  .then((unlock) => {
    // Unlock after 1s
    setTimeout(unlock, 1000);
  });
```

#### delayed(delay: number): Promise

* `delay` the delay in ms
* return a Promise resolved with tLOCKEDhe unlock callback if the mutex success to lock.

Try to lock the mutex and resolve the promise.
The mutex can be unlocked only in a interval of `delay` ms.

The interval is computed when the method is called so you can unlock the mutex before the end of the interval with
`mutex.unlock()` or
invoke an othe `mutex.delay(delay: number)` with a lesser delay.

You should have a mutex for every feature and not reuse a mutex for others.
It is useful to keep performance on polling functions.

Example:

```javascript
let iterations = 0;
const interval = setInterval(() => {
  mutex.delayed(200)
    .then(() => {
      if (iterations++ === 5) {
        clearInterval(interval);
      } else {
        console.log(iterations);
      }
    });
}, 100);
```
