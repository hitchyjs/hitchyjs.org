---
title: Processing Configuration
sorting: 3
---

### `configure()`

After discovery of extensions Hitchy is reading and merging all configuration files included with application. After this Hitchy is notifying all extensions for optional validation and normalization of configuration data.

#### Profile

* **stage:** configuration
* **purpose:** 
  * validate and/or normalize application configuration
* **knowledge:**
  * Hitchy's core API plus
    * compiled configuration in `api.runtime.config`
  * runtime options
  * collected information on current extension (incl. its folder, name, meta information and API)
* **processing order:** dependency-based
* **supported types:** 
  * common module function pattern
