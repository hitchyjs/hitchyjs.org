# Performance Matters

Javascript is a scripting language and thus won't ever compete with native code such as written in C, C++ or similar. Nevertheless it is capable of doing amazing things in no time as long as all code is trying to stick with patterns that have proven to show high performance. Here are some rules to follow on implementing Hitchy:

* Cache frequently used information fetched from a source requiring more complex access. In fact, accessing property of an object in a local variable twice might be sufficient reason to read this property once writing into another local variable and use that local variable instead.

* Cache results of previous iterations.

* Don't use arrays with callbacks due to wasting remarkable time per every item of an array. 

  * Using simple for-loops is best to iterate over arrays.

  * When iterating over arrays multiple stages of processing can be combined resulting in a lot less memory consumed for intermittent results, as well.

  * Pretend required data and prepare with sparse arrays if possible. 
  
  So, instead of
  
  ```ecmascript 6
  const result = source.map( a => parseInt( a ) ).filter( a => a > 100 ).slice( 0, 10 );
  ```
    
  use this a lot more complex code which is performing much better though:
    
  ```ecmascript 6
  const numSource = source.length;
  const result = new Array( numSource );
  let write = 0;

  for ( let read = 0; read < numSource; read++ ) {
    const num = parseInt( source[read] );
    if ( num > 100 ) {
      result[write++] = num;
      if ( write >= 10 ) {
        break;
      }
    }
  }

  result.splice( write );
  ```

     * This code has the potential to never iterate over all elements of `source`. The former variant is iterating over all elements twice before taking another copy of limited size. 
    
    * Instead of invoking two callbacks for every element in `source` the revised approach isn't ever invoking any callback. 
    
    * Using sparse array in second variant prevents arrays from being copied over and over due to its increasing size. We know the maximum size of resulting array and so prepare sufficient space initially. Note that this approach has its limits when it comes to very large arrays.

* Use as much synchronous code in a row as possible, but start [processing asynchronously](performance.md) as soon as information isn't available yet. 

  For example, when going to return a promise from a function you should validate provided arguments before entering any asynchronous code rather than starting with a promise.
  
  This is bad:

  ```ecmascript 6
  function someProvider( source ) {
    return new Promise( ( resolve, reject ) => {
      if ( typeof source !== "string" || !source ) {
        reject( new TypeError( "invalid source" ) );
      }
    
      // TODO implement your code here

      resolve( result );
    } );
  }
  ```
  
  This is better:

  ```ecmascript 6
  function someProvider( source ) {
    if ( typeof source !== "string" || !source ) {
      return Promise.reject( new TypeError( "invalid source" ) );
    }
    
    return new Promise( resolve => {
      // TODO implement your code here

      resolve( result );
    } );
  }
  ```
