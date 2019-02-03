# API

The term _API_ refers to an _application's programming interface_. In context of Hitchy the term is slightly ambiguous.

1. Hitchy consists of a core exposing basic functionalities resulting in what is called _Hitchy's Core API_. It's described in more detail in a [reference manual](ref/).
2. However, extensions to Hitchy's core to be discovered by the latter may each expose their own API.
3. Hitchy's core is eventually merging its own API with the ones exposed by discovered extensions resulting in a comprehensive API considered _Hitchy's API_.

## Hitchy at Runtime

Hitchy's core is designed to do a good job on discovering extensions and integrating them with the core as good as possible. It is compiling APIs as exposed by any discovered extension merging them with its own. Finally it is exposing the resulting API as `this` whenever it invokes a function which is part of either extension or your current project such as an individual request handler.

```javascript
function( req, res ) {
	const api = this;
	
	// access API to handle the request
}
```

::: tip
Make sure to get this right: Hitchy's core is designed to always expose its compiled API when invoking some function as described here, but either extension's code must not stick with this rule by design. Consider using closure scopes to access the API then.
:::

::: tip
Don't miss the [common module pattern](../conventions/cmp.md) or the [common module function pattern](../conventions/cmfp.md) to learn how to establish proper context in a module for accessing Hitchy's API at runtime.
:::
