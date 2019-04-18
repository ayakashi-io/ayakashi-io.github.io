---
layout: default
title: Anonymous props and prop references
parent: Going Deeper
nav_order: 6
---

# Anonymous props and prop references

## Anonymous props

If no name is passed to any of the select methods when defining a prop,
an internal id will be used as the prop's name.  
Most of the time you should give a name to your props. This makes it easier to reference them later by their name.  
An anonymous prop might be better though if the prop is only going to be used as a parent (with `from()` or chaining).

## Getting and using a prop's reference

All select methods return a prop object which can be used instead of the string name of a prop

```js
const mainSection = ayakashi
    .select("mainSection")
    .where({
        id: {
            eq: "main"
        }
    })
```

The variable `mainSection` can be used instead of the string name `"mainSection"` in any action or extraction.  
This is especially useful for anonymous props since they don't have a recognizable string name.  
<br/>
We can also get a prop reference at any point by using its string name

```js
const mainSection = ayakashi.prop("mainSection");
```

The `prop()` method can also be used to check if a prop actually exists and is valid since it will return `null` if not.
