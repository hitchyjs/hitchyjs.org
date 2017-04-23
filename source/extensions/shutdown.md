---
title: Releasing Resources on Shutdown
sorting: 7
---

### `shutdown()`

This method is expected to implement counterpart to `initialize()` described before.

On properly shutting down hitchy application it first closes all open client connections. After that it shuts down application by notifying all extensions in reverse dependency-based order using this optionally provided API method. Due to reversing order of extension processing any extension that was initialized last on bootstrap gets shut down first now.

#### Profile

* **stage:** application shutdown
* **purpose:** 
  * release resources requested on initializing extension
* **knowledge:**
  * hitchy's full API incl. core, configuration and elements
  * runtime options
  * collected information on current extension (incl. its folder, name, meta information and API)
* **processing order:** reverse dependency-based
* **supported types:** 
  * common module function pattern
