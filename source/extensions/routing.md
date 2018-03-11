---
title: Setting Up Request Routing
sorting: 6
---

### `routes` and `policies`

After initializing extension and gaining access on backends to be available through some extension it is time to declare routes mapping incoming requests onto controllers for generating proper responses.

Every extension may provide one or two separate maps for matching incoming requests and select method in controller or policy to process the request. First map is for policy routing and is given in API property `policies`. The second one is regarding responder routing and is given in API property `routes`.

> Either map may be given literally (thus being defined on loading extension) or as a function complying with common module function pattern to generate map now. The latter case enables definition of routes depending on application's actual context.

#### Profile

* **stage:** router configuration
* **purpose:** 
  * (compile and) provide map of request URL patterns into selection of methods to invoke for filtering requests (_policies_) or for responding (_responders_)
* **knowledge:**
  * Hitchy's full API incl. core, configuration and elements
  * runtime options
  * collected information on current extension (incl. its folder, name, meta information and API)
* **processing order:** dependency-based
* **supported types:** 
  * object
  * [common module function pattern](concepts/common_patterns.html#Common-Module-Function-Pattern-CMFP)
    
#### Important Note on Routing

Request dispatching doesn't obey order of routes given by single extension. Thus any extension shouldn't rely on routes being processed the same way they are listed in source code, but carefully choose request URL patterns for uniquely matching. On one hand that's due to improving organization of routes for fast processing. That's way routing maps are organized as objects in javascript. On the other hand properties of objects can't be enumerated in a reliable order. That's due to specification for implementations of javascript engine.

Routes of different extensions are processed in dependency-based order of extensions.
