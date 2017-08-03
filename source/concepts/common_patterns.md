---
title: Supported Common Coding Patterns
date: 2017-04-23 16:51:22
sorting: 4
categories:
- Concepts
tags:
- CMP
- CMFP
---

## Common Module Pattern (CMP)

In several situations Hitchy supports particular pattern for providing additional data and/or functionality. This pattern is available 

* for injecting components into your Hitchy setup, 
* for providing custom configuration in your project's configuration related to Hitchy and 
* in all models, controllers, policies and services of your project and its extending components.

In either case some module is loaded using `require()`. Any such _module_ might provide requested data as such which is "the common way" of exporting its API. However, the more beneficial way is to export a single function that is expected to return that requested data. 

You are advised to stick with the latter technique to gain full access on API of Hitchy and options used to start it. In addition the module becomes able to return promise for providing requested data with delay. For example, discovery process or compilation of project's configuration is delayed until some returned promise is resolved with eventually provided information.

A short example of Hitchy's common module pattern looks like this:

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
		configure: function() {
			
		}
	};
};
```

By following this pattern access on Hitchy's API is provided through closure variable `api` here. This includes all found configuration, any model or controller etc. Thus, when documentation refers to some variable or data available as `api.foo.bar` this is referring to the access granted here. In addition `options` is available to access configuration provided by application or runtime environment injecting Hitchy. E.g. on using Hitchy's internal server this includes any custom command line option.

> **Important Issue:** On bootstrapping Hitchy-based application several libraries and utilities are loaded most of them sticking with this pattern. On complying with this pattern those libraries are copies bound to provided instance of Hitchy API. Because of this loading a library multiple times results in multiple copies of library code.
>
> **Solution:** CMP as a pattern is provided to simplify extensibility of Hitchy. This disadvantage can be handled though by using library components loaded on bootstrap and exposed as part of Hitchy's API instance itself. For example, always prefer using `api.utility.file` over `require( "hitchy/lib/utility/file" )` or similar.

> **Note:** On implementing types such as classes CMP must not be used to keep supporting class inheritance and `instanceof` operator. Please separate implementation of such basic types and any code relying on it and make the latter comply with CMP, only!  


## Common Module Function Pattern (CMFP)

As a derived concept functions might be in compliance with _common module function pattern_ by satisfying these criteria:

1. It **must** expect reference on `api` via its `this` variable.
2. It **must** expect reference on `options` in first argument.
3. It **might** expect any _additional arguments_ succeeding that first one.
4. It **might** return Promise instance to delay actual result. This **may** suspend overall processing for relying on this function's result.

In several cases a component complying with Common Module Pattern is exporting functions via this pattern which comply with Common Module Function Pattern and thus are called with references on `api` and `options`, too. Though this is slightly redundant by design it establishes more flexibility in designing components and modules since modules mustn't comply with Common Module Pattern as a whole, but still might provide function complying with this pattern. In addition passing references locally might result in a slightly better performance due to preventing deeply accessing closure scope variables.
