---
title: Adding Custom Routes 
date: 2017-04-23 16:51:29
---

# About Routes

Hitchy's core comes with a very powerful routing engine which might serve well in many different situations. Let's start simple nevertheless ...

Hitchy is suitable for matching some request handling code to one or more request patterns e.g. used by some browser or your VueJS-based single-page application. A route is actually mapping from a request pattern into the description of a function to be invoked for handling matching requests or into that function itself. All routes are tested on a request's path name, only.

Hitchy supports two kinds of routes: 

* Terminal routes are expected to create response to be sent back to client. Any handler matching some terminal route is invoked and expected to end response. You can't have multiple handlers invoked per terminal route.

* Policy routes provide some sort of opposite. A single request might pass several matching policy routes and either matching handler may adjust or end the response. Policy routes may be processed before or after terminal routes with the latter designed to e.g. clean up resources used by terminal routes or similar.

# Configuration

In your project's configuration (and here we talk about that Hitchy project which might be located inside your other project, only) you may declare routes very easily.

The project's configuration is read from files in subfolder **config** of your Hitchy-based project. So create that folder, put a new file **server/routes.js** in it with some code like this:

```javascript
module.exports = {
	routes: {
		"/": function( req, res ) {
			res.send( "Hello World!" );
		},
	},
};
```

> Actually, the name of the file doesn't matter. For sake of usability you should call it **routes.js** as it is exposing a section of resulting configuration called `routes`.

Restart your hitchy service: press Ctrl+C if it's running in a console and restart it just like given before. Reload your browser and see this:

![](/images/hitchy-hello-world.png)

This very simple example is instructing Hitchy to invoke provided request handler as soon as there is a request for URL path name `/` which is the same as requests providing no path name at all.

This example is pretty simple. And you might want to play around with the route's source (which is the `"/"` in example). Try different path names and see how Hitchy is responding.

> Don't forget: any change to your Hitchy-based project requires you to restart the service. 

## Method Matching

The source `/` or any other path name is matching requests of browser accordingly. That's okay since browsers request services using GET method by default. As soon as you are querying the service using any other HTTP method such as POST, PUT or similar this route won't be matching anymore.

In fact any such source consists of two parts separated by space with the first one being optional:

1. the HTTP method (defaults to GET as mentioned before)

2. the path name of URL

Any route has to match both parts to be processed. Using special HTTP method `ALL` or `*` a route is matching any HTTP method.

> This applies to terminal routes as well as policy routes.

## Parameter Matching

Hitchy's matching of request pathes relies on [path-to-regexp](https://www.npmjs.com/package/path-to-regexp). This supports parameter matching to be available and request handlers via `req.params`.

Try declaring a terminal route like this:

```javascript
routes: {
	"/greet/:name": function( req, res ) {
		res.send( "Hello " + req.params.name + "!" );
	},
},
```

Querying http://localhost:3000/greet will result in Hitchy's error view due to request missing required parameter in second segment of URL path. Querying http://localhost:3000/greet/john result in this view, however:

![](/images/hitchy-greeting-john.png) 

# Request Handlers

The request handler works quite similar to ExpressJS. _Terminal_ handlers like the one given before are invoked with two arguments `req` (for _request_) and `res` (for _response_). The first is describing received request. The second is available to create a response sent back to the client (a.k.a. browser or your VueJS application or ...). 

* Either parameter is basically identical to those provided by HTTP server implementation of NodeJS and so you should read those docs for [req](https://nodejs.org/dist/latest/docs/api/http.html#http_class_http_incomingmessage) and [res](https://nodejs.org/dist/latest/docs/api/http.html#http_class_http_serverresponse) if you aren't familiar with them.

* When your project is hitched onto some existing ExpressJS framework all extensions to either argument as provided by ExpressJS for [req](https://expressjs.com/en/4x/api.html#req) and [res](https://expressjs.com/en/4x/api.html#res) are available here as well.
 
* If you have started a Hitchy-based project without ExpressJS - as described in this tutorial - there are some extensions to the basic functionality provided by NodeJS. Those extensions are inspired by ExpressJS and try to mock signatures used by ExpressJS as good as possible.

  * `res.send()` is sending some provided data always implicitly ending current response. The format indicated in response header depends on type of data provided here:
  
     * Strings are sent as `tex/plain`.
     
     * Objects are sent as `application/json`.
     
     * Instances of `Buffer` are sent as `application/octet-stream`.
     
     * Any other type of data is ending response without actually sending some data but marking response to be `application/octet-stream`.
     
  * `res.json()` is sending some provided object formatted as JSON implicitly ending current response.
  
  * `res.status()` is adjusting HTTP status code send with response without ending it implicitly.
  
  * `res.set()` is available to set one or more variables of response header. You might provide one variable with name and value in separate arguments or an object describing several header variables in sole argument.
  
  * `res.type()` adjusts content type of responsing either by providing MIME identifier such as `image/jpeg` or `text/html` or by using some supported shortcut such as `text`, `xml`, `png` etc.
  
  * `res.format()` takes an object mapping MIME identifier of encoding accepted by client into functions invoked to provide a response matching either format. Special property `default` might be used to provide a responder for all formats not listed explicitly.
  
  * `res.redirect()` responds with a redirection to a different URL given as argument. It is implicitly ending response.
 
## Policy Handlers

While terminal routes are handled by terminal handlers policy routes are handled by policy handlers. These work with a slightly different signature by taking three arguments `req`, `res` and `next`. The first two are equivalent to those provided to terminal handlers. But the last one is a callback that needs to be invoked as soon as the particular handler has finished processing request.

This is pretty equivalent to ExpressJS. But since we love to work with promises you may return a promise from your policy handler, too, to mark the time when its done.

> Actually you may return promise from a terminal handler, too, e.g. when it's creating complex response that might take some time. Remember that there might be some policy handlers invoked after your terminal handler has finished responding. By returning promise from a terminal handler such late policy handlers will be deferred and thus won't interfere with the processing of your terminal handler.

# Policy Routes in Configuration

Policy routes may be declared similar to terminal routes declared in example above. Create a file **server/config/policies.js**:

```javascript
policies: {
	"/throttled": function( req, res ) {
		return new Promise( resolve => {
			setTimeout( resolve, 5000 );
		} );
	},
},
```

After restarting Hitchy opening URL http://localhost:3000/throttled in your browser will show the error on missing request page as before, but this time it will take about 5s to appear.

## Prefix-Based vs. Exact Matching

Next add another route to declaration in **server/config/routes.js**:

```javascript
routes: {
	...,
	"/throttled/response": function( req, res ) {
		res.send( "Here I am!" );
	},
}
```

This terminal route will handle requests for URL path name `/throttled/response`. After restarting Hitchy and opening that URL in browser the related terminal handler responds with `Here I am!`. But this response is still deferred by about 5s. That is because policy routes always apply to all requests matching some prefix whereas terminal routes are required to match exactly. That's why

* requesting `/throttled` is responding with Hitchy's error view after 5s,

* requesting `/throttled/response` is responding with `Here I am!` after 5s,

* requesting `/throttled/response/extra` is responding with Hitchy's error view after 5s again and

* requesting `/response` or `/response/extra` are responding with Hitchy's error view instantly.

## Early vs. Late Policy Routes

As mentioned before policy routes may be processed before or after any matching terminal route. Early policy routes are processed before terminal routes and might filter request parameters, inject authentication and session management, prepare required resources etc. Late policy routes are less common and considered to release resources used by any matching terminal route or prepared by some early policy route. In addition you could use late policy routes for logging statistical information on time spent with responding to some request.

Previous definition of a policy route was considered early policy route implicitly. By tweaking provided configuration you could declare early and/or late policy routes explicitly:

```javascript
policies: {
	before: {
		"/throttled": function( req, res ) {
			return new Promise( resolve => {
				setTimeout( resolve, 5000 );
			} );
		},
	}
},
```

This is explicitly declaring an early policy route to be obeyed prior to processing any terminal route. Accordingly the following example is declaring the same policy route as late policy route:

```javascript
policies: {
	after: {
		"/throttled": function( req, res ) {
			return new Promise( resolve => {
				setTimeout( resolve, 5000 );
			} );
		},
	}
},
```

Try this one! Restart Hitchy and open URL http://localhost:3000/throttled/response again. This time the service is responding instantly due to 5s delay is applied _after_ response has been sent.

# Controllers vs. Policies

tbd.

# What About Providing Some Data Model? 

Head over to the next chapter showing how to [add some data model](/getting-started/odm.html)!
