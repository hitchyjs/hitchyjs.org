# A core worth to be called as such

The hitchy core is very limited by means of supported functionality. It provides code for

* implementing CLI and being customized through runtime options.
* optionally injecting itself as a middleware into a runtime built with ExpressJS rather than starting its own server listening for HTTP requests.
* discovering and integrating extensions in as many situations as possible.
* handling incoming requests according to some defined routes, even though there isn't any defined route except for some error handling by default.
* providing basic utilities commonly useful in many situations such as handling file access and generating logging output.

That's all Hitchy's core is doing. There is no custom business logic and there is no awareness of data models to be managed for different kinds of access. Just the bare minimum ready to do everything.
