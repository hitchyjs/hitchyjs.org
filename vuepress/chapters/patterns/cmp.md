# The Common Module Pattern (CMP)

This pattern applies to code modules exporting some interface consisting of functions and/or data.

In several situations Hitchy relies on modules to be integrated for customizing its behaviour. This applies

* on injecting components into your Hitchy setup, 
* on providing custom configuration in your project's configuration related to Hitchy and 
* in all models, controllers, policies and services of your project and its extending components.

In either case some module is loaded using `require()`. Any such module might provide requested data as such which is "the common way" of exporting its API. However, the more beneficial way is to export a single function that is expected to return that requested data. That's called the _common module pattern_ in Hitchy.

## Example

Consider the following module using regular exporting:

```javascript
module.exports = {
	// here comes your actual data 
	configure() {
		// add your configuration code here
	},
	someInfo: "the value",
};
```

This module can be easily converted to comply with common module pattern:

```javascript
/**
 * Provides implementation for second stage of bootstrapping Hitchy instance.
 *
 * @this HitchyAPI
 * @param {HitchyOptions} options
 * @returns {function(modules:HitchyComponentHandle[]):Promise.<HitchyComponentHandle[]>}
 */
module.exports = function( options ) {
	const api = this;
	
	return {
		// here comes your actual data 
		configure() {
			// add your configuration code here
		},
		someInfo: "the value",
	};
};
```

Instead of exporting your API you simply export a function that is returning your API. This function is invoked in context of Hitchy's API. Options used to start Hitchy are passed in first argument.

## Benefits

1. Your code is gaining access on Hitchy's [API](../api/). This includes all found configuration, any model or controller etc.

   ::: tip
   When documentation refers to some variable or data available as `api.foo.bar` this is referring to the access granted here. See the example above declaring `api` as a closure-scoped variable for use in any exported function.
   :::

2. Provided `options` are exposing custom configuration provided by application or runtime environment injecting Hitchy. On using Hitchy's internal server this includes any custom command line option.

3. By default any compliant module may return its interface as-is or promising it by returning instance of Promise. This is very useful in case your module requires some asynchronous setup first. Any counterpart supporting this pattern must be aware of this opportunity. That's why using common module pattern enables plugins to defer bootstrap very easily. 

::: danger
On bootstrapping Hitchy-based application several libraries and utilities are loaded most of them sticking with this pattern. On complying with this pattern those libraries are copies bound to provided instance of Hitchy API. Because of this **loading a library multiple times results in multiple copies of library code**.

CMP is designed to simplify extensibility of Hitchy focusing on support for Promises returned from function exported by complying module. The resulting risk of code duplications can be mitigated by accessing all components loaded on bootstrap and exposed as part of Hitchy's API via the API instance itself. 

For example, instead of using `require( "hitchy/lib/utility/file" )` for accessing a module of Hitchy's library you should always use `api.utility.file`.
:::

::: tip
On implementing types such as classes CMP must not be used to keep supporting class inheritance and `instanceof` operator. Please separate implementation of such basic types and any code relying on it and make the latter comply with CMP, only!  
:::

&rarr; [Common Module Function Pattern](cmfp.md)
