---
title: The Hell of Dependencies
menuitem: On Dependencies
sorting: 2
---

## Dependencies Aren't Bad in Itself, But ...

As of 2017 [NodeJs](http://nodejs.org) is very popuplar. This popularity is driven by the sheer amount of "extensions" easily available to install with Node's package manager [npm](http://npmjs.org). Obviously, all you need is the proper set of extensions and some glue code to stick them all together. This way creating prototypes of new applications is a matter of minutes. It sounds pretty amazing.

Most frameworks show how to quickstart with a blog or some todo list. Have you ever checked the number of files and folders involved in this pretty simple use case when it's done? Have you ever wondered why starting new application in SailsJS might take minutes to complete? Depending on a single framework does not keep your application from mediately depending on thousands of files "installed" to quickstart your _simple_ application. Simple "Hello World" in Express isn't quite as simple as it might be.

People consider this less significant, but by depending on a high number of extensions any application becomes highly dependent. It's an obvious matter of fact. And it isn't bad in itself until it's time to upgrade some of those extensions your software relies on. Just consider some security vulnerability fixed in a recent release of one of your framework's extensions. How to fix that if your framework doesn't upgrade to comply with a more recent version of its dependency as fast as you like it to do?

Nowadays it is pretty common to have short term software life cycles. Quite often "Long Term Support" covers up to 24 months, only. And every six month new major upgrades are released probably introducing severe incompatibilities with any previous release. 

Do the math! How much time do you spend with creating application, negotiating with customer on required features and finishing initial release to become productive? How much time is left before you need to adopt upgraded APIs of your extensions? Will you keep up with that if your customer is going to use your application for several years? Will you tell him how much this will cost right at the beginning?


## Dependencies Negatively Impact Performance

Depending on extensions _usually_ requires invocation of functions provided by those extensions. As illustrated [before](concepts/es6.html) this has a negative impact on performance though this might be less remarkable due to actual use case of an extension.

But do you know all code of every extension your application is going to rely on? Do you know the skills of those who wrote it? Have you checked if any used extension is unnecessarily wasting time having a severe impact on your application's performance?


## Conclusion

We definitely don't deny any use of extensions. But take care of what extensions your application relies on and whether this relation is actually worth the impact it has on your application's performance and maintainability.

Feel responsible for what dependencies you make your application rely on. And always consider to replace an extension with a custom implementation of that single feature you like to use. In the end coding isn't Lego.

> As a matter of fact this mistrust in previously used frameworks lead us to starting development of our own. We aren't that keen to do everything all ourselves, but this way we can basically take responsibility for what we deliver.
