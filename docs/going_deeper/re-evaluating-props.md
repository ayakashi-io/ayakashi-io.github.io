---
layout: default
title: (re)evaluating props
parent: Going Deeper
nav_order: 7
---

# (re)evaluating props

You might have already noticed this but the select methods are pretty much the only methods in Ayakashi's API
that don't return a promise (no `await`).  
This happens because props are lazy and are only evaluated the first time they are used, either by being passed to an action
or used for extraction.  
<br/>
**After a prop is evaluated, it is cached for any subsequent use.**

## Re-evaluating a prop

There might be a need to re-evaluate a prop if a page is dynamically updating its content.  
Since props are cached after their first use you would get stale or incorrect results in this case.  
We can use the prop `update()` method to mitigate against that

```js
const mainSection = ayakashi
    .select()
    .where({
        id: {
            eq: "main"
        }
    });

//use the mainSection prop
//...

//after some interaction (scroll, click etc) the page updates the #main element dynamically
//...

mainSection.update();

//use the mainSection prop again
//...
```

## Manually evaluating a prop

You can also evaluate a prop manually by using the `trigger()` prop method

```js
const mainSection = ayakashi
    .select()
    .where({
        id: {
            eq: "main"
        }
    });

await mainSection.trigger();
```

This is actually what most of the actions and `extract()` do internally.  
You can also use `trigger({force: true})` to re-evaluate the prop if is is already evaluated and cached.
