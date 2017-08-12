---
title: Routing Requests
sorting: 10
---

## Routing in Hitchy: An Introduction

Hitchy comes with a custom request processing engine. This engine was primarily inspired by [SailsJS](http://sailsjs.org).

### Routing Phases

On starting any Hitchy-based application its core is discovering all required extensions resulting in a certain order of processing these extensions at any time. This same order is obeyed on collecting route configurations of every discovered extension and combining them with each other and with custom routes of current application into common routing lists to be processed on every incoming request.

At first Hitchy is collecting all configurations of routes in groups each related to a single source and sorts those in compliance with order of extensions. These groups are ordered like this:

1. There is a leading group for all custom routes of current application to be processed **early**.
2. Next there are separate groups per included extension in same order as extensions each containing routes of either extension to be processed **before** some default action.
3. After that there is another group of custom routes of current application to be processed **before** any default action.
4. Then there is a group of routes considered implementing any default action. This group is used to contain all routes designated to _blueprint actions_.

All these groups contain configuration of routes commonly preceding or (eventually) representing any default action. Thus they are considered routes of routing phase named **before**. A second set of groups are commonly considered to configure routes of routing phase named **after**:

1. First there is a group for all custom routes of current application to be processed right **after** any default action.
2. Then there are separate groups per included extension each containing routes of either extension to be processed **after** some default action. This time these groups are sorted in reverse order compared to discovered order of extensions.
3. Finally there is a group for all custom routes of current application to be processed as **late** as possible.

> **Note:** By reversing order of extension-related groups in routing phase `after` routes establish proper nesting of extensions' scopes when it comes to request processing. 
>
> For example, consider some extension declaring _policy routes_ used to take time of request processing. This has to start taking time as early as possible but stop timer as lately as possible. 
>
> In another example some extension might adjust request context to be used with every extension succeeding this one due to dependencies. When it comes to reverting this special request context this shouldn't happen before having processed all `late` routes of those extensions processed in modified context during `before` phase or after having processed `late` routes of extensions that haven't been processed in modified context in `before` phase previously.

There are two pairs of such ordered groups in every Hitchy application as routes may be configured for filtering requests and for responding.

* The former case is related to choosing policies to include with request processing and thus those routes are called _policy routes_. 

* The latter kind of routes is checked when it comes to generating some response. Since processing of these routes terminates on first route matching any current request they are called _terminal routes_.

Finally this results in four lists of groups processed in order like this:

1. policy routes of **before** routing phase
2. terminal routes of **before** routing phase
3. terminal routes of **after** routing phase
4. policy routes of **after** routing phase

> **Note:** Due to checking for terminal routes terminates on first matching route the actual dispatching might be skipping the third list containing terminal routes of `after` routing phase.

#### Routes of a Single Group

In any group described before configuration of routes may be given in one of three ways:

* An array of objects combines definition of a route's source and its target in every single item.
* An instance of `Map` can be used to map from source descriptors into target selectors while obeying order of given mappings.
* A regular object might be used to simplify definition and reading of configuration. It is also considered to contain properties each mapping from source descriptors into target selectors. _Due to the nature of regular objects configurations might not be processed in order given in configuration._

### Configuring Routes

Every route is configured by describing its source and assigning a target to be invoked on a request matching the route.

#### Describing a Route's Source

Every route require's a source to be matched by a request so the route is used to process the request. A source usually consists of two parts:

1. an HTTP method
2. a path name or some pattern describing the path name with injected parameters and optional parts

While the provision of an HTTP method is optional the path name is mandatory.

The source may be given as a string or as an object, though use of string is required when configuration multiple routes using instance of `Map` or regular object.

##### Source as a String

On providing a route's source as string it must comply with this schema:

```
[ <method> ] <path-name-pattern>
```

This means:

* On providing an HTTP method it has to precede the provision of path name. It must be separated from the latter using one or more whitespace characters. Thus the `<method>` can't contain any whitespace. And the `[` and `]` aren't meant to be given literally but indicate fact of `<method>` being optional.
* The path name may be any pattern to be supported by [path-to-regexp](https://www.npmjs.com/package/path-to-regexp) which is intended to support Express-style path name patterns in turn.

If provision of HTTP method is omitted the special method **ALL** is used by default. This special method is requesting to process the route without respect to the request's HTTP method.

##### Source as an Object

A source's method and/or path name may be given in an object as well using two separate properties for either information:

* Property `type` may be used to provide optional HTTP method route is to be bound to.
* Property `url` is used to provide path name pattern to be matched.

Either property must be given as string.

> **Note:** On providing multiple routes in an array these properties are combined with properties selecting the route's target satisfying the requirement to have one item in array per route to be configured. That's why `type` is used to select the HTTP _method_.

#### Selecting a Route's Target

A route's target is usually a function exposed by a controller or policy. Thus _selecting a target_ means to address or provide the function to be invoked on every request matching the route's source. This can be achieved in one of three ways:

##### Target as a Function

Of course it is possible to provide the target as a function reference. This option is available due to every other option will be converted into this kind. However this way isn't considered optimum due to reduced code readability and less flexibility regarding function overloading using extensions etc.

##### Target as a String

A target may be named by combining name of a policy (when used on a policy route) or controller (when used on a terminal route) with the name of static method exposed by that policy or controller. Both parts may be combined using single period or double colon. 

> The name of policy or controller may optionally include related suffix `Policy` or `Controller`. This name is always used lowercase and so is case-insensitive.

##### Target as an Object

As with sources any target may be provided using regular object with certain properties providing same information as a string described before:

* Property `controller` is considered naming policy or controller to be used.
* Property `method` is naming the method to be exposed statically by the selected policy or controller.

In addition this object may contain a property `args` providing an array of arguments to be passed on invoking target additionally.

This way of selecting a target may be merged with properties describing source as given before. This merging is required to provide an array of route configurations with each item of array describing source and target of a route. 

#### Configuring Routes per Extension

Extensions describe their individual routing in hooks named `policies`, `routes` and `blueprints`.

Providing any of these hooks is optional. Either hook takes a single list of routes to be bound to some default phase. Optionally hooks `policies` and `routes` may use separate lists to explicitly bind routes to one of the supported phases. Route configurations given in hook `policies` are used for _policy routing_ and those given in hooks `routes` and `blueprints` are used for _terminal routing_.

* On providing single list in a hook this one is considered to be used in routing phase **before** default action.  
   ```javascript
   ...
   routes: {
       "GET /user/login": "UserController::login",
       ...	
   },
   ...
   ```

* By providing two lists any extension can provide separate lists for routing phases **before** and **after** default action. In this case lists are provided as properties `before` and `after` of a regular object.  
   ```javascript
   ...
   routes: {
       before: {
           "GET /user/login": "UserController::login()",
           ...	
       },
       after: {
           "GET /user/:id": ErrorHandler.userNotFound,
           ...
       }
     }
   },
   ...
   ```

Either list of routes may be given as array, instance of `Map` or regular object as described before. 

In compliance with CMFP either hook itself may be given as function to be invoked for returning the actual data, too. This enables dynamic creation of route configurations. Any such function might return promise to delay provision of route configuration, too. Finally it is also possible to provide a promise instead of function in the first place.

```javascript
...
policies: function( options ) {
    let api = this;

    return Promise.resolve( {
        before: {
            "/user/login": "Session::create()",
            ...
        },
        after: [ 
            {
                url: "/user/login",
                controller: "Session",
                method: "create",
            },
            ...
        ]
    } );
},
routes: function( options ) {
    let api = this;

    return new Map( [
        [ "GET /user/login", "UserController::login()" ],
        ...	
    ] );
},
...
```

| Hook         | Type of Routes  | Optionally Supported Phases | Default Phase |
|--------------|-----------------|-----------------------------|---------------|
| `policies`   | `PolicyRoute`   | before, after               | before        |
| `routes`     | `TerminalRoute` | before, after               | before        |
| `blueprints` | `TerminalRoute` | -                           | blueprint     |

#### Configuring Custom Routes of Application

The custom routes of current application must be provided in its configuration under `routes` and `policies` for configuring custom routes for _terminal routing_ and _policy routing_ respectively. 
 
Either list of route configurations may be given as array, instance of `Map` or regular object as described before.

> An application can't configure custom blueprint routes by intention.

### Routes At Runtime

On compiling routes from configuration every definition of a route is converted into an instance of class `Route`. By default all _policy routes_ are represented by instances of `PolicyRoute` and all _terminal routes_ are represented by instances of `TerminalRoute`.

> **Note:** Even though it is possible to put instances of `TerminalRoute` into lists included with policy routing and vice versa you are hereby strongly discouraged to use it as this is untested and might result in unintended behaviour of your application. 
>
> Configurations should stick with _describing_ routes instead of providing readily created instances of `Route`.

#### Prepared for Dispatching

Hitchy's core is preparing efficient request processing by first merging all groups of route configurations as described before into single sorted lists of configurations. According to explanations above this results in four lists of routes with two of them used on policy routing and the other two on terminal routing.

All routes in every such list are then grouped by HTTP method each route is optionally bound to. This results in sublists of routes each bound to a single HTTP method. During this process all unbound routes are merged into all other lists bound to some HTTP method. This merging keeps track of original order of route processing.

Eventually every sublist is processed in a similar way to group its route by longest static prefix with more generic routes distributed over any list related to a more specific prefix.

> **Note:** Due to this grouping by prefix requests can be processed much faster if you prefer a higher number of routes with more specific prefix over a lesser number of routes with more generic prefix.

#### On Dispatching

Due to this preparation the dispatching of routes becomes pretty performant. Any request is bound to some HTTP method and thus only the related sublist per phase must be processed. In every such sublist the prefix-bound group with the longest prefix matching current request is then used to check every included route eventually.

As mentioned before this routing is passing 3 or 4 out of 4 phases given before:

1. Any request is passing all _policy routes_ in routing phase **before** covering path name of current request. These routes are considered to filter requests, extending request context for any route processed afterwards.
2. Next all _terminal routes_ in routing phase **before** are checked for the first one matching path name of current request. If matching route is found here the dispatching is skipping next step.
3. If no _terminal route_ was matching **before** all _terminal routes in routing phase **after** are checked for the first one matching path name of current request.
   > In theory these two steps regarding terminal routes might be considered single step, too.
4. After having checked all _terminal routes_ the list of _policy routes_ in routing phase `after` are checked for covering path name of current request. This happens no matter some matching terminal route has been found before or not. 
   > Since response might be generated before responding in a _policy route_ here isn't good practice unless it's used for providing error pages or similar. You should always check for having response to current request prior to generating response in a late policy route.
