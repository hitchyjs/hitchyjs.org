---
title: ECMAScript 6
menuitem: ECMAScript 6
---

## Modern Tools Come With Modern Language

[ES6](http://es6-features.org/) is the next generation of Javascript language and it is mostly [supported in current LTS releases of NodeJS](http://node.green). Hitchy was designed to run server-side using NodeJS and there is no intention or even some kind of sense in running hitchy in the ambiguous world of browsers. Therefore it is time to start using ES6 and all its benefits! 

As a concept or paradigm of Hitchy this doesn't _require_ anyone to replace existing skills in coding with Javascript by all the neat features that come with ES6. But it doesn't demand to keep code downward compatible with any older engine either. In addition there is no need to use transpilers or similar to convert modern Javascript into plain old Javascript. And since ES6 comes with class inheritance, destructuring, default parameters, promises, generators, iterators etc. there is no need to compile code written in some fancy Javascript-like language into Javascript to be run with NodeJS.

### Quest For The Best Mix

When using new features of Javascript it is always worth taking into account their impact on performance. For example, Javascript greatly suffers from deeply nesting code into functions called recursively. Same applies to utilizing iterators invoking same callback for every item found on iterating larger arrays, lists or similar. Sometimes sticking with good old for-loop is far better than reducing code to one-liners without knowing what is happening under the hood.

#### An Example

Let's consider this easy-to-read code:

```javascript
let result = someArray
	.filter( i => i )
	.map( value => processorFn( value ) )
	.slice( 0, 10 );
```

It's easy to see what's going on there. But how fast is it in case of `someArray` containing 1000, 10.000 or even 100.000 elements? And how does it compare to the following code doing the very same thing?

```javascript
let length = someArray.length;
let limit  = Math.min( 10, length );
let result = new Array( limit );
let write  = 0;

for ( let read = 0; read < length && write < limit; read++ ) {
	let value = someArray[read];

	if ( value ) {
		result[write++] = processorFn( value );
	}
}

result.splice( write, limit - write );
```

Both implementations achieve the same result. But the latter does it very fast. And yes, the latter code suffers from readibility thus resulting in hardened maintenance of code. But that's what (proper) unit tests are meant to support.

> ### Anecdote
>
> We've seen Javascript code finding about 500.000 (half a million) occurrences of a small string of up to 3 bytes in a 140 MByte haystack string and mapping that set of occurrences into unique set of text line indexes containing those occurrences within less than 500ms on a higher-value 2015 embedded system just because of sticking with performance as described before. 
>
> Using iterators and callbacks same search took several minutes to complete.

In the end the optimum should depend on the actual impact on performance: code running just a few times on boot might use iterators whereas code involved in processing every request to process lists should stick with a more complex, faster approach.
 
Speaking of callbacks and performance this brings us to [another concept of Hitchy](concepts/dependencies.html).
