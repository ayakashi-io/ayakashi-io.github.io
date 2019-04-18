---
layout: default
title: Evaluating javascript expressions
parent: Going Deeper
nav_order: 1
---

# Evaluating javascript expressions

While Ayakashi comes bundled with all kinds of high level actions it also allows executing arbitrarily
javascript expressions in the context of the currently active page.

```js
const title = await ayakashi.evaluate(function() {
    return document.title;
});
```

## Passing parameters

```js
const title = await ayakashi.evaluate(function(property) {
    return document[property];
}, "title");
```

Any number of parameters can be passed and they will be available in the function in the order that they are passed.

```js
const title = await ayakashi.evaluate(function(arg1, arg2, arg3) {
    // arg1 === 1
    // arg2 === 2
    // arg3 === 3
}, 1, 2, 3);
```

## Current scope is not transferred

Since `evaluate` will serialize the function and its arguments and then execute it inside the chromium instance,
the current scope is lost and is **not** transferred.  
**The following won't work**:

```js
const myVariable = "hello";

await ayakashi.evaluate(function() {
    console.log(myVariable); // => Error!
});
```

## Any type of argument can be passed

You can pass any type of argument to the evaluated function and it will be properly serialized.

* primitive values (numbers, strings etc)
* objects and arrays
* functions
* regexes

## Evaluating asynchronous functions

Asynchronous functions work by using `evaluateAsync()` instead of `evaluate()` and by returning a promise:

```js
await ayakashi.evaluateAsync(function() {
    return new Promise(function(resolve) {
        setTimeout(function() {
            resolve();
        }, 1000);
    });
});
```

## Wrapping evaluations in a custom action

If you keep using an `evaluate()`, consider wrapping it in a [custom action](/docs/advanced/creating-your-own-actions.html).

## Evaluating large functions

`evaluate()` works great for small functions but if you find yourself evaluating large functions
consider creating a library and load it as a preloader.  
This will help diminish the serialization and transferring cost (since it will only happen once) and also improve the readability
of your scrapper.  
You can learn how [here](/docs/advanced/creating-your-own-preloaders.html).