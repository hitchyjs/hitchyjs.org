---
title: Phases of Bootstrapping Extensions
sorting: 1
---

## Profile Summary

| property         | stage          | knowledge              | dependency-based order | supported types |
| ---------------- | -------------- | ---------------------- | ---------------------- | --------------- |
| _loading_        | discovery      | Core, Options          | no                     | object or CMP   |
| `$meta`          | discovery      | -                      | no                     | object          |
| `onDiscovered()` | discovery      | Core, Options          | yes                    | CMFP            |
| `configure()`    | configuration  | Core + Config, Options | yes                    | CMFP            |
| `onExposing()`   | exposure       | Full API, Options      | yes                    | CMFP            |
| `onExposed()`    | exposure       | Full API, Options      | yes                    | CMFP            |
| `initialize()`   | initialization | Full API, Options      | yes                    | CMFP            |
| `policies()`     | router setup   | Full API, Options      | yes                    | object or CMFP  |
| `routes()`       | router setup   | Full API, Options      | yes                    | object or CMFP  |
| `shutdown()`     | shutdown       | Full API, Options      | yes, reversed          | CMFP            |

### Remarks

Extension loading passes several stages to be explained in more detail on separate pages. This brief summary is providing basic information on either stage.

* **property** names elements exposed by either extension for integrating with Hitchy's core in either stage.

* **stage** is the name of a stage as described on separate pages. On regarding extension loading and procesisng Hitchy's bootstrap is passing listed stages in order given here.

* **knowledge** lists information that are available in either stage.

  * **Core** refers to API of Hitchy's core being available.
  
  * **Options** refers to available access on runtime options.
  
  * **Config** marks compiled configuration to be available.
  
  * **Full API** indicates that whole API is available. This includes APIs of all discovered extensions.

* **dependency-based order** marks whether extensions are processed in proper order as derived from calculated tree of dependencies or not.

* **supported types** shows supported types of data related property might be provided as.

  * **object** indicates that the property may be given as object, only.
  
  * **[CMP](concepts/common_patterns.html#Common-Module-Pattern-CMP)** and **[CMFP](concepts/common_patterns.html#Common-Module-Function-Pattern-CMFP)** enable properties to be given as functions matching particular signature and behaviour described on linked pages. 

