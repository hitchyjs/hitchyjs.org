---
title: Exposing Components of All Discovered Extensions
sorting: 4
---

### `onExposing()` and `onExposed()`

Next hitchy enters exposure stage to collect and compile elements of extension API. Those elements are divided into _models_, _controllers_, _policies_ and _services_. Extensions don't have to provide such elements at all. If multiple extensions use same name for some kind of element versions are merged successively.

First hitchy is invoking every extension's optionally provided callback **onExposing()** to dynamically define or extend elements of listed types. This hook is expected to expose elements as properties of `api.runtime.models`, `api.runtime.controllers` etc.

Next hitchy is enumerating files in certain folders of every extension to contain files each defining or extending one of the elements listed above.

* An extension's models reside in folder `api/models`.
* An extension's controllers reside in folder `api/controllers`.
* An extension's policies reside in folder `api/policies`.
* An extension's services reside in folder `api/services`.

> On defining any such element hitchy does not qualify models or inject routes to controllers automatically. Instead an application might require extension taking care of that.

Element extension is supported on files complying with common module pattern. This enables access on API exposed so far including elements defined before. Previously exposed elements are available as properties of `api.runtime.models`, `api.runtime.controllers` etc.

Finally hitchy is invoking every extension's optionally provided callback **onExposed()** for probably adding and/or modifying elements just like **onExposing()** before.

#### Profile

* **stage:** exposure
* **purpose:** 
  * qualify definitions of models, controllers, policies and services (e.g. by extension introducing basic support for models or similar)
* **knowledge:**
  * hitchy's full API incl. core, configuration and recently compiled elements in `api.runtime.models`, `api.runtime.controllers`, etc.
  * runtime options
  * collected information on current extension (incl. its folder, name, meta information and API)
* **processing order:** dependency-based
* **supported types:** 
  * common module function pattern
