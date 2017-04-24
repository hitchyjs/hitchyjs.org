---
title: Providing Basic Information on Extension
date: 2017-04-23 16:51:22
sorting: 3
---

## Meta Data File

Every component needs to provide a file **hitchy.json** in its root folder. This file is used by Hitchy to detect this component and expect it to be compatible with its bootstrap process. Contained data is qualified on loading (e.g. to include some default values). It is available via special property `$meta` of components API which in turn is promoted via `api.components`.

> **Example:** Component hitchy-foo has meta data file containing this:
> ```JSON
    { 
      "role": "foo", 
      "custom": "info" 
    }
```
> At runtime any code is capable of accessing this information using `api.components.foo.$meta.custom` or similar.

Basically the file might be empty by means of containing empty JSON-encoded object.
 
```JSON
{}
```

Of course the file actually contains some information in most cases. Here is a brief list of supported properties:

### role

This string property explicitly declares the role filled by component. By default this is derived from basename of folder containing component (and thus this **hitchy.json** file). By explicitly providing role here component might declare to fill certain role different from component's name. This is useful to promote the component for replacing another one.


### dependencies

This optional list of strings lists all roles of Hitchy components this current component depends on. This list is used to ensure all required components are available. In addition it affects the order of bootstrapping components.
