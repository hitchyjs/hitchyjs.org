---
title: Discovering Extensions
sorting: 2
---

## Finding and Loading Extensions

All extensions get loaded on hitchy's bootstrap. Thus all extensions are loaded prior to handling first request. Any folder in your project's **node_modules** is tested for containing **hitchy.json** file and considered hitchy extension then. This file must be located in root folder of extension and contains the extension's _static meta information_.

Hitchy is loading any extension by "requiring" its folder. Thus you must provide a main script named **index.js** in root folder of your extension or name any other main script in your extension's **package.json** file.

> Every extension must provide main script file to be loaded. This main script may be empty by means of absolutely blank text file.

Here comes a brief profile of _extension loading_:

#### Profile

* **stage:** discovery
* **purpose:** (or _what is it good for in an extension_)
  * detect available extensions
  * choose role to fill eventually
  * provide _dynamic meta information_ (see section on $meta below)
  * _provide API of extension_
* **knowledge:** (or _what info is available to the extension_)
  * hitchy's core API
  * runtime options
  * names of all basically available extensions
  * static meta information (as defined in extension's **hitchy.json** file)
* **processing order:** arbitrary
* **supported types:**
  * object
  * common module pattern

## An Extension's Basic API

Every extension is considered to expose information through several properties usually complying with common module function pattern. 

> Any extension may omit some or even all of the properties listed here. This results in certain default behaviour.

Here comes a list of API properties obeyed by hitchy bootstrap process.

### `$meta`

This property is processed on loading extension. It is merged with meta information read from **hitchy.json** file before.

> $meta is always an object. It can't be function complying with common module function pattern. This doesn't hurt as the API itself may be provided by function on extension complying with common module pattern.

#### Profile

* **stage:** discovery
* **purpose:**
  * adjust meta information depending on current context, e.g.
    * select role to fill
    * select actual dependencies of extension
* **supported types:** 
  * object

#### On Roles

Extensions are required to fill a certain role, which is simply a name given as string. Roles are important for managing dependencies between extensions. By resolving dependencies hitchy is establishing certain order applied to all further processing involving extensions.

There are three kinds of roles:

1. Every extension is assigned implicit role on missing any explicit declaration. This implicit role is equivalent to the extension's name which in turn is just the basename of folder containing it.
2. In **hitchy.json** file role of extension may be declared explicitly as property **role**. 
3. The extension may provide dynamic meta information including property **role** as well.

While the two former are called _static role_ of extension, the latter is called its _dynamic role_. On processing roles of extensions dynamic role takes precedence over static ones, explicitly declared one over implicit one.

##### What if Two Extensions Declare the Same Role?

_In scope of an application every role may be filled by single extension, only._ 

However, multiple extensions may statically declare to fill the same role as long as one of them is declaring that role dynamically, too. Dynamic role of one extension might clash with static roles of other extensions actually revoking static roles of those. Extensions without any role won't be processed any further. 

Finally two extensions must not claim to fill the same role dynamically.

##### What if Role is Revoked from Extension?

Whenever dynamic role of one extension causes revocation of same role statically declared by another one the latter extension doesn't fill any role anymore unless it declares one dynamically. 

> Due to arbitrary processing of extensions in this stage trying to declare role dynamically after having lost static role may be considered bad practice.

Extensions without role won't be processed after **onDiscovered** (see below). Hitchy won't obey them on configuring or initializing extensions and on preparing routes. It won't be exposed as part of Hitchy's runtime API. However, any replacing extension might use it for extending its functionality.

##### What is it Good for?

Roles are used to find extensions on resolving dependencies of either extension. Dynamic roles are useful to check current application's scenario first by inspecting list of available extensions. By declaring role dynamically one extension can replace another one transparently e.g. for extending the latter one's functionality.

#### On Dependencies

Extensions may list roles of other extensions they rely on. Hitchy is using this definition of dependencies to apply certain order for processing extensions further on. Any extension gets processed after all its dependencies.

Any application relying on hitchy may choose roles filled by extensions as its initial dependencies. This way hitchy does not load all extensions filling a role but those actually required as immediate as well as mediate dependencies of application.

### `onDiscovered()`

An extension may expose function to be called after having finished discovery of extensions.

#### Profile

* **stage:** discovery
* **purpose:** 
  * save access on replaced extensions e.g. for extending their functionality
* **knowledge:**
  * hitchy's core API
  * runtime options
  * roles filled by extensions eventually
  * references on APIs of all previously discovered extensions (including those having lost their role)
  * collected information on current extension (incl. its folder, name, meta information and API)
* **processing order:** dependency-based
* **supported types:** 
  * common module function pattern
