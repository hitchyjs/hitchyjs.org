---
title: Preparing Resources
sorting: 5
---

### `initialize()`

Every module may provide custom initialization handler e.g. to finally connect to some data source or sync model definitions with schema of data source.

> As a counterpart to this `shutdown()` may be used to release resources requested here.

#### Profile

* **stage:** initialization
* **purpose:** 
  * initialize instance of extension in context of current application runtime (e.g. link to database, etc.)
* **knowledge:**
  * Hitchy's full API incl. core, configuration and elements
  * runtime options
  * collected information on current extension (incl. its folder, name, meta information and API)
* **processing order:** dependency-based
* **supported types:** 
  * common module function pattern
