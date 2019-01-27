# Dependencies: Sheep or Wolves

::: warning Disclaimer
The modules mentioned in this post have been chosen randomly for illustration purposes, only. This doesn't imply any ranking or similar assessment regarding their quality or security.
:::

[npm](https://www.npmjs.com/) is a tool offering access on a huge amount of libraries provided for free by the open source community. The vast majority of modules over there depend on each other. And this isn't bad in itself.

## Profanity

Quite often though dependencies become ridiculously simple. Some implement very basic features such as [extending objects](https://www.npmjs.com/package/xtend) or [padding strings](https://www.npmjs.com/package/left-pad). Such packages tend to have LICENSE and README files taking more disk space than the actual code. We consider those modules to be highly inefficient not to say harmful. 

## Security or Naive Trust In Skills

Their high popularity account to the oddities of npm ecosystem and to the proof that many developers approach software development like a Lego construction kit, thus showing less skills and more simplicity regarding trust in foreign code. These oddities grow with every module depending on those ones. In the end you might create a business application that strongly relies on modules created by inexperienced or even underaged software developers.

Using dependencies requires trust in skills of either module's author. Maintaining your code always requires to keep an eye on the code of your dependencies. The more modules your code depends on the harder it gets.

::: tip Example
As of now - January 2019 - module [xtend](https://www.npmjs.com/package/xtend) mentioned before hasn't been updated for three years but [its code](https://github.com/Raynos/xtend/blob/master/immutable.js#L5) is vulnerable to [prototype poisoning attack](https://stackoverflow.com/questions/38712990/javascript-object-prototype-poisoning). In addition, there are numerous [modules depending on xtend](https://www.npmjs.com/browse/depended/xtend) and so it's currently downloaded more than 8 million times a week. There are more than 2500 modules depending on it including another very popular module called [through2](https://www.npmjs.com/package/through2) which has more than 10 million weekly downloads and 10k modules depending on it in turn. 

Of course, the vulnerability of xtend doesn't _imply_ a vulnerability in through2, but you can't exclude this implication either without checking code of through2 first. So, are you sure through2 developers are aware of this vulnerability in their dependency?
::: 

## Obstruction of Maintenance

The example above illustrates another issue with deeply depending modules as there might be a significant impact on your application's long-term maintainability. 

* What if your code relies on a module that is depending on a module vulnerable to some attack? 
* What if that vulnerable module isn't maintained anymore at all? 

To fix this vulnerability as it is affecting your code you need to upgrade all modules in between your code and the vulnerable module as well as the latter one. 

The same applies to cases when a deep dependency of your code has encountered _and fixed_ a severe vulnerability but you can't upgrade because some module between your code and the fixed module isn't properly maintained and updated timely. This might be due to the need of switching to a more recent major release of the fixed module which is exposing different API that needs to be adopted first. And the maintainer of the module in between has no intention of spending this time. This way your application is stuck with vulnerable software.

## Degrading Performance

With dependencies your code tends to use function invocations more often. With regards to performance you should consider whether using dependency is affecting your application's performance. 

::: tip Example
Instead of using a module like [left-pad](https://www.npmjs.com/package/left-pad) to pad your strings you could try very simple inline code instead. Instead of running

```javascript
leftPad( someString, 20 );
```

you could use

```javascript
String( "                    " + someString ).slice( -20 )
```

Try running either approach 100k times and you'll see the effect on performance.
:::

## Feature Redundancy

Several dependencies had been eligible in times when NodeJS didn't provide a certain feature natively. This applies to promises or async/await for handling asynchronous processing. And even by introducing native support in NodeJS neither of those modules stopped being useful implicitly. But in many cases their benefit isn't worth the hassle anymore regarding issues with security and maintainability mentioned before.

::: tip Example
[through2](https://www.npmjs.com/package/through2) has been mentioned before. In recent releases of NodeJS the support for declaring streams passing through or transforming data with streams has been strongly improved. It is a well documented and quite manageable task. Using a dependency like through2 is left with adding very little benefit to some application over using native support for declaring custom streams. With respect to the statements above it might even harm security and maintainability.
:::

## Conclusion

We don't basically reject dependencies. But the decision to use one should always follow certain considerations and decisions. They cover topics specific to our code:

* How many times is my code using code of a dependency in general and during some certain time? Is it thus critical to performance?
* Do I know how to implement it myself?
* Is there native support included with recent releases of my Javascript engine working similar to the feature provided by a module?

And they regard module-specific questions:

* Is there an active community maintaining the dependency?
  * When was the last time an update has been published?
  * Are there any open issues with recent comments by reporter of issue and author of module?
* How many projects rely on it?
* On how many modules is it depending? And how many dependencies has any of these.
* Is it well tested? What is its code coverage?

The latter set of questions is to be repeated for every dependency that my module or its dependencies depend on.
