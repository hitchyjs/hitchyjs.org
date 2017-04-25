---
title: Managing Asynchronous Processing
menuitem: Promises
sorting: 3
---

## Befriend With Promises

There are many different ways for managing asynchronous processing in your code. And you might encounter some online controversy about using promises or not. We think promises are best concept for handling asynchronous processing while managing fault tolerance. 

By relying on recent LTS releases of NodeJS [native support for promises](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Promise) is available. That's why we didn't make [BluebirdJS](http://bluebirdjs.com) a dependency of Hitchy though we consider it to be the best promise library in the field. Instead we try to cope with native API as good as possible and add some selected helpers to Hitchy's core ourselves.

### How To Work With Native API

Promises are created using constructor with callback generating what is promised.

```javascript
new Promise( function( resolve, reject ) { ... } )
```

Native support for promises includes instance methods `Promise#then()` and `Promise#catch()`. In addition class methods `Promise.all()` and `Promise.race()` are available to start multiple asynchronous processes in "parallel".

> Whenever multiple asynchronous processes have to be run consider using `Promise.all()` instead of chaining promises of either process.

#### Spreading Arguments

Promises can deliver single value, only. But this isn't limited to scalars. So it is common pattern to use arrays to deliver multiple values. Native API doesn't require `Promise#spread()` as provided by BluebirdJS. Destructuring function arguments work pretty well instead.

```javascript
Promise.all( [
	firstPromise,
	secondPromise,
	thirdPromise
] )
	.then( function( [ firstResult, secondResult, thirdResult ] ) {
		// TODO use firstResult etc. here ...
	} );
```

#### Available Helpers

Hitchy provides several helpers in namespace `utility.promise` of its API.

| promisified helper                           | somewhat equivalent to |
| -------------------------------------------- | ---------------------- |
| `api.utility.promise.each( set, fn )`        | `set.forEach( fn )`    |
| `api.utility.promise.filter( set, fn )`      | `set.filter( fn )`     |
| `api.utility.promise.map( set, fn )`         | `set.map( fn )`        |
| `api.utility.promise.multiMap( set, fn )`    | `set.map( fn )`        |
| `api.utility.promise.find( set, fn )`        | `set.find( fn )`       |
| `api.utility.promise.indexOf( set, fn )`     | `set.indexOf( fn )`    |
| `api.utility.promise.delay( delay, result )` | `setTimeout()`         |

A callback invoked by any such helper is enabled to return promise for delaying iteration of array. In addition you may iterate array-like data as well.

#### Promisifying APIs of NodeJS

Currently, except for some filesystem-related utilities there is no such option included with Hitchy.

Filesystem-related helpers are

* `api.utility.file.list( pathname )`
* `api.utility.file.stat( filename )`
* `api.utility.file.read( filename, readOptions )`
* `api.utility.file.readMeta( filename, readOptions )`

As you might expect each of these functions is returning a promise.
