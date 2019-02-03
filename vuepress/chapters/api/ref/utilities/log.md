# Logging

Logging in Hitchy is available via `api.log()`. This function is invoked to gain access on a named logging facility. Hitchy supports enabling logging per facility.

If you want to generate logging invoke `api.log()` with a name referring to your module or the context it is working in. This name of a logging facility is basically an arbitrary string that _should_ comply with these conventions which are an extension to the according [conventions of **debug**](https://www.npmjs.com/package/debug#conventions):

* It consists of lowercase latin letters and dashes, only (a.k.a. [kebab-case](https://en.wikipedia.org/wiki/Letter_case#Special_case_styles)).
* It may use colon to support namespaces.
* The namespace `hitchy:` should be used by Hitchy, only.

## Example

```javascript
const Logger = api.log( "my-component" );

Logger( "write this in another log entry" );
```

Currently, logger relies on [debug](https://www.npmjs.com/package/debug). Thus you can use [printf](https://www.npmjs.com/package/debug#formatters)-like placeholders to inject additional data into your string:

```javascript
const Logger = api.log( "my-component" );

Logger( "write %s in another log entry", "this" );
```

::: tip
It's basically okay to unconditionally generate log entries in your code for customizing generated logging output is available per [runtime configuration](#runtime-configuration). 

Generating huge amounts of logging will have an impact on performance, though, by generating additional stack frames probably not resulting in any actual logging output.

Thus, don't keep all your noisy debug logging in production code.
:::

## Runtime Configuration

All logging is sent to stderr of running Hitchy application. Using superdaemons such as systemctl that output is persisted by the controlling superdaemon.

### Internal Server Node

Running your Hitchy application via its internal server node the available logging output can be controlled via CLI parameters

#### Enable All Logging

Using `--debug` will enable all logging unconditionally.

#### Select Enabled Facilities

The CLI option `--log-levels=<names>` expects `<name>` to be a comma-separated list of facility names. This list supports [wildcards](https://www.npmjs.com/package/debug#wildcards). You can prefix wildcards and facility names with a dash to explicitly exclude those facilities from list of enabled ones.

In addition the environment variable DEBUG is obeyed.

### Integrating with **express**

Hitchy's internal server node is basically managing environment variable `DEBUG`, only. Thus manually setting the latter will affect Hitchy's logging when integrated with [express](https://www.npmjs.com/package/express) or similar.

### Programmatic Control

Since `api.log` is a reference to module **debug** enabling facilities may be controlled and tested programmatically, too: 

```javascript
api.log.enable( "facility-name" );
api.log.disable( "facility-name" );

if ( api.log.enabled( "facility-name" ) ) { ... }
```
