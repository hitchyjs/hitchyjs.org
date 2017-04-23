---
title: Phases of Bootstrapping Extensions
sorting: 1
---

## Profile Summary

| property | stage | knowledge | dependency-based order | supported types |
| --- | --- | --- | --- | --- |
| _loading_ | discovery | Core, Options | no | object or CMP |
| `$meta` | discovery | - | no | object |
| `onDiscovered()` | discovery | Core, Options | yes | CMFP |
| `configure()` | configuration | Core + Config, Options | yes | CMFP |
| `onExposing()` | exposure | Full API, Options | yes | CMFP |
| `onExposed()` | exposure | Full API, Options | yes | CMFP |
| `initialize()` | initialization | Full API, Options | yes | CMFP |
| `policies()` | router setup | Full API, Options | yes | object or CMFP |
| `routes()` | router setup | Full API, Options | yes | object or CMFP |
| `shutdown()` | shutdown | Full API, Options | yes, reversed | CMFP |
