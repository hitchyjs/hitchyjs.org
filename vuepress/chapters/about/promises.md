# Asynchronous Code with Promises

There are a lot of approaches to conveniently and safely handling asynchronous code. 

* For example, you can use callbacks just like API of NodeJS does. But these aren't quite safe to use and offer very little convenience and flexibility. 

* You can use generator functions for that. But that's more like a hacky workaround. 

* [Reactive Extensions](http://reactivex.io/) provide a popular option e.g. used with Angular. However, these aren't natively supported by Javascript but require some pretty complex dependency.

* There are promises. They are specifically designed for this job. They help with collecting errors for central handling. They support additional listeners registered for the information deferredly provided by promises even after that information has become available.

* There is a new ES6 syntax called **async / await** trying to simplify writing of asynchronous code. This approach is as eligible as promises and might be a revision over the latter. In fact, ``async / await`` is syntactic sugar implemented with promises internally.

* Further libraries are available in the wild, implementing one cool pattern or the other. But they all lack native support in Javascript. As far as we are concerned this is an impediment for using either one. That's not because of we dislike those approaches. There is simply no need to have a whole set of dependencies helping with implementing something that's natively supported by Javascript nowadays as well. It is a pretty valid example for [assessing pros and cons of relying on a dependency](dependencies.md) prior to start depending on it eventually. 

  In addition we don't deny to reject Reactive Extensions as used in Angular. Though they might provide a more flexible approach in several cases they are a lot harder to read making code harder to understand. And they tend to provide weird approaches to handling errors which is due to very specific use cases considered while defining such patterns as streams, observables and subjects.

Following this shallow insight we decided to stick with promises. Whenever there is a lack of information this information gets promised. And whenever a callback gets invoked e.g. to integrate some extension this callback may return promise to request the caller to be patient until the promised information has become available. This greatly improves performance of Hitchy on handling extensions without loosing on behalf of flexibility.
