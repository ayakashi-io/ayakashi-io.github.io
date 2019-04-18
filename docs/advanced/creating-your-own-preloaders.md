---
layout: default
title: Creating your own preloaders
parent: Advanced
nav_order: 3
---

# Creating your own preloaders

Advanced
{: .label .label-red }

This is an advanced section on how to create your own preloaders.  
If you haven't done so already, you may want to read [the preloaders tour](/docs/guide/tour.html#preloaders)
and how to [load 3rd party libraries as preloaders](/docs/going_deeper/loading-libraries-as-preloaders.html)
to get a better idea.  
<br/>
There is no much to say about preloaders since they are just standard javascript code we preload on a page.
(before any of the page's code has begun loading).  
There is no special syntax or API we need to register a preloader but there are two different ways a preloader can be executed.

## Generating a preloader

Ayakashi includes a command to generate a new preloader, let's use it.  
Inside your project's root folder, run:

```bash
ayakashi new --preloader --name=myPreloader
```

This will create a `preloaders` folder and our `myPreloader.js` preloader inside it.  
`myPreloader.js` contains the following example content:

```js
//console.log(navigator.userAgent);
//or export it as a module to be available on window.ayakashi.preloaders
//and execute it on demand
// module.exports = function() {
//     console.log(navigator.userAgent);
// };
```

Which pretty much explains everything we need to know.  
We can either have our preloader be a standard script that will get executed on load
or export a module which we can then later use in our
[evaluate()](/docs/going_deeper/evaluating-javascript-expressions.html) calls by accessing `ayakashi.preloaders.myPreloader`.
