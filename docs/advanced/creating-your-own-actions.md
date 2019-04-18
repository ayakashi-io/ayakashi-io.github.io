---
layout: default
title: Creating your own actions
parent: Advanced
nav_order: 2
---

# Creating your own actions

Advanced
{: .label .label-red }

This is an advanced section on how to create your own actions, either from scratch or by re-using and depending upon existing
actions (both builtin and custom made).  
If you haven't done so already, you may want to read [the action tour](/docs/guide/tour.html#actions)
and the [builtin actions](/docs/reference/builtin-actions.html) section to see what's already bundled and get some ideas.

## Generating a new action

Ayakashi includes a command to generate a new action, let’s use it.
Inside your project’s root folder, run:

```bash
ayakashi new --action --name=myAction
```

This will create an `actions` folder and our `myAction.js` action inside it
with some boilerplate to get us started.

```js
/**
 * @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
 */
module.exports = function(ayakashi) {
    ayakashi.registerAction("myAction", async function(prop) {
        //prop boilerplate
        const myProp = this.prop(prop);
        if (!myProp) throw new Error("<myAction> needs a valid prop");
        const matchCount = await myProp.trigger();
        if (matchCount === 0) throw new Error("<myAction> needs a prop with at least 1 match");
        //do something with the prop
    });
};
```

New actions are registered with the `registerAction()` method which takes a name for the new action
and a function with the action's code.  
The generated content also includes some standard boilerplate to handle props in the new action.  
Read a bit about how props [behave](/docs/going_deeper/re-evaluating-props.html)
and prop [references](/docs/going_deeper/anonymous-props-and-references.html) if you are planning to use a prop in your action.

### Prop boilerplate

The boilerplate does the following:

* gets a [reference](/docs/going_deeper/anonymous-props-and-references.html) to the prop we are passing with the `prop()` method
* throws an error if the prop is not defined
* it [triggers]((/docs/going_deeper/re-evaluating-props.html)) our prop, which evaluates its query to get its matches.
* throws an error if no matches were found by its query

## Creating a complete action

Let's create a complete action called `markMatches()` which will take a prop and
add a red border around all of its element matches to help us see what our queries are
doing while we are developing our scrapper.

Let's generate our file:

```bash
ayakashi new --action --name=markMatches
```

Here is the complete code:

```js
/**
 * @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
 */
module.exports = function(ayakashi) {
    ayakashi.registerAction("markMatches", async function(prop) {
        const myProp = this.prop(prop);
        if (!myProp) throw new Error("<markMatches> needs a valid prop");
        const matchCount = await myProp.trigger();
        if (matchCount === 0) throw new Error("<markMatches> needs a prop with at least 1 match");
        await this.evaluate(function(propId) {
            ayakashi.propTable[propId].matches.forEach(function(el) {
                el.style.border = "4px solid red";
            });
        }, myProp.id);
    });
};
```

After our standard boilerplate we use the [evaluate](/docs/going_deeper/evaluating-javascript-expressions.html) method and
pass it our prop's `id`.  
Inside `evaluate()` we are now in the page's (browser) scope.  
We use the `propId` we just passed to get a reference to the actual prop from the `propTable`.  
We then just iterate upon all of its matches and add our red border style to each element.

## Create new actions by composing existing actions

In our example we created an action from scratch but we can re-use existing actions in our new action as well.  
Here is how the builtin [typeIn()](/docs/reference/builtin-actions.html#typeIn) action (which inserts text in inputs)
is implemented:

```js
ayakashi.registerAction("typeIn", async function(prop) {
    const myProp = this.prop(prop);
    if (!myProp) throw new Error("<typeIn> needs a valid prop");
    const matchCount = await myProp.trigger();
    if (matchCount === 0) throw new Error("<typeIn> needs a prop with at least 1 match");
    if (!text) throw new Error("<typeIn> needs some text to type");
    await this.scrollIntoView(myProp);
    await this.focus(myProp);
    //...text insertion code
});
```

First we have the standard boilerplate.  
Then we use the [scrollIntoView()](/docs/reference/builtin-actions.html#scrollIntoView)
and [focus()](/docs/reference/builtin-actions.html#focus) actions to scroll the element into view and focus on it.  
Then follows the actual text insertion code (skipped for brevity).

## Not all actions need a prop

So far all of our example actions accepted a prop and used the standard boilerplate to handle it.  
But that doesn't have to always be the case.  
An action can pretty much do anything we wish. It's just a way to package our functionality and re-use it
in a controllable way.  
So if an action doesn't need a prop we can just go ahead and remove the prop boilerplate.
