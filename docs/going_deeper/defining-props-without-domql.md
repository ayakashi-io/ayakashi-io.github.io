---
layout: default
title: Defining props without domQL
parent: Going Deeper
nav_order: 8
---

# Defining props without domQL

[domQL](/docs/guide/querying-with-domql.html) is pretty powerful and easy to use but sometimes we might need a bit more control
on how we want to define a prop.  
For this case we can use `defineProp()`

```js
ayakashi.defineProp(function() {
    return this.document.getElementById("main");
}, "mainSection");
```

**Make sure to return a html element (or multiple elements in an array)**.  
We can then use the new `mainSection` prop like any other prop.  
[Standard prop behaviour](/docs/going_deeper/re-evaluating-props.html) also applies to props created with `defineProp()`.

**Note**: It's good practice to not reference `window` or `document` directly
but instead get access to them through `this`.  
That way the props can be re-used by any kind of scrapper without depending on the environment.
