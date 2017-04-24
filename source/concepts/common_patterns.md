---
title: Supported Common Coding Patterns
date: 2017-04-23 16:51:22
sorting: 1
---

### Common Module Pattern

In several situations hitchy supports particular pattern for providing additional data and/or functionality. This pattern is available for injecting components into your hitchy setup, for providing custom configuration in your project's configuration related to hitchy and in all models, controllers, policies and services of your project and its extending components.

In either case any such _module_ might provide requested data as such. However, the more beneficial way is to export a single function that is expected to return that requested data. You are advised to stick with the latter technique to gain full access on API and options used to start/inject hitchy. By exporting function component you might even return promise to provide requested data with delay. For example, discovery process or compilation of project's configuration is delayed until some returned promise is resolved with eventually provided information.

A short example of hitchy's common module pattern looks like this:

```javascript
/**
 * Provides implementation for second stage of bootstrapping hitchy instance.
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

By following this pattern access on hitchy's API is provided through closure variable `api` here. This includes all found configuration, any model or controller etc. Thus, when documentation refers to some variable or data available as `api.foo.bar` this is referring to the access granted here. In addition `options` is available to access configuration provided by application or runtime environment injecting hitchy. E.g. on using hitchy's internal server this includes any custom command line option.


#### Common Module Function Pattern

As a derived concept functions might be in compliance with _common module function pattern_ by satisfying these criteria:

1. It **must** expect reference on `api` via its `this` variable.
2. It **must** expect reference on `options` in first argument.
3. It **might** expect any _additional arguments_ succeeding that first one.
4. It **might** return Promise instance to delay actual result. This **may** suspend overall processing for relying on this function's result.

In several cases a component complying with Common Module Pattern is exporting functions via this pattern which comply with Common Module Function Pattern and thus are called with references on `api` and `options`, too. Though this is slightly redundant by design it establishes more flexibility in designing components and modules since modules mustn't comply with Common Module Pattern as a whole, but still might provide function complying with this pattern. In addition passing references locally might result in a slightly better performance due to preventing deeply accessing closure scope variables.
