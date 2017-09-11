---
title: Core Principles
menuitem: Core Principles
sorting: -10
---

## Let's Start With Some Buzzwords!

Each buzzword refers to some core principle that we believe every software should obey.

### Performance

Hitchy is designed primarily to perform as good as possible. Of course, better performance implies higher consumption of memory. So the balance is essential. Performance basically results from preventing unneccessarily frequent processing of asynchronous tasks and deep and/or frequent stack frames.

### Responsiveness

When it comes to asynchronous tasks Javascript unveils its secret power by actually permitting asynchronous processing. That's why Hitchy work synchronously as long as possible, but takes care of such tasks causing delay and works with promises for proper handling.  

### Fault Tolerance

Software may fail. But most failures don't need to kill an application. Expect failures whenever using "external" stuff. And in a world of asynchronous processing promises are essential to simplify error handling and improve fault tolerance this way.

### Extensibility

By starting simple but extensible your software won't waste time on things some of its users don't ever require. 

The Hitchy core exists of two major parts. One part takes care of [bootstrapping](runtime/bootstrap.html) to discover and merge all _required_ extensions. The second part routes requests coming from a framework Hitchy was hitched to into handlers of extensions discovered before. Aside from that there are basic tools available for coding extensions more conveniently.

### Convention Over Configuration

Don't rely on configuration (e.g. to be read from files) when it is much easier to start a convention and demand to stick with it.

### Maintainability

Neither Hitchy nor applications to be build with Hitchy aren't designed to die soon again. Hitchy isn't made for prototyping, but for production use. This implies code to be maintainable. And so code should be as readable as possible without having an impact on performance. Annotations and code documentation are great tools to help with that.

Just consider this documentation website: it wasn't made some time when Hitchy was stable enough to spend some time on documenting stuff. Documentation is no add-on to any software. It was an integral part of development since the very beginning to help us keeping focus on what to do and how we'd like it to be done.
