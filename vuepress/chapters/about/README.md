# About

HitchyJS is yet another server-side framework entirely written in Javascript. 

It's basically inspired by [Sails](https://sailsjs.com/) trying to overcome several disadvantages we've encountered there while creating applications relying on it. We've created several web applications relying on HitchyJS now thus we might have missed recent revisions in Sails. Please keep this in mind when judging us for pointing out benefits of HitchyJS over its muse.

HitchyJS offers a highly extensible approach. That's why it starts with a "simple" core which is pretty dumb on its own. The core implementation focuses on these intentions:

* [Do as little as possible yourself.](core-features.md)
* [Provide highly flexible interfaces for injecting extensions.](extensions-api.md)
* [Keep an eye on performance.](performance.md)

In addition the overall HitchyJS development sticks with these paradigms:

* [Prefer conventions over configuration.](coc.md)
* [Work synchronously unless a lack of information might cause idle processing.](asynchronous.md)
* [Prefer promises for handling asynchronous processing.](promises.md)
* [Stick with modern syntax.](modern-syntax.md)
* [Rarely use dependencies.](dependencies.md)

Some of these paradigms seem to oppose intentions listed before at first. But on second glance they are quite complementary.
