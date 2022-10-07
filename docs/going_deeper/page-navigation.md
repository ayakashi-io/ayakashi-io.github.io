---
layout: default
title: Page navigation
parent: Going Deeper
nav_order: 5
---

<!-- markdownlint-disable MD022 -->
# Page navigation
{: .no_toc }
<!-- markdownlint-enable MD022 -->
Including the standard `goTo()` method, Ayakashi has some more functions to help
with navigation in normal and single page application (SPA) pages.

<!-- markdownlint-disable MD022 -->
## Table of contents
{: .no_toc .text-delta }
<!-- markdownlint-enable MD022 -->

* Standard navigation
* Click to navigate
* Single page application (SPA) navigation
* Using the raw events
{:toc}

## Standard navigation

To navigate to a page and wait until the `DOMContentLoaded` has been fired, use `goTo()`

```js
await ayakashi.goTo("https://ayakashi-io.github.io");
```

You can also specify a larger timeout (in ms) if the page takes too long to load (default is 10s):

```js
await ayakashi.goTo("http://slow-page.com", 20000);
```

You may use a timeout of `0` to disable the timeout.

## Click to navigate

Many times there is the need to click a link or button to navigate.  
We can use the `navigationClick()` action for that

```js
ayakashi
    .select("myLink")
    .where({id: {eq: "theLink"}});
await ayakashi.navigationClick("myLink");
```

`navigationClick()` needs a [prop](/docs/guide/tour.html#props) to click.  
It will click the prop and then wait for the `DOMContentLoaded` event of the new page to fire.  
There is an optional timeout you can specify (in ms) as a second parameter (default is 10s).  
You may use a timeout of `0` to disable the timeout.

## Single page application (SPA) navigation

When working with single page applications, since there are no page reloads going on, we should use
the `spaNavigationClick()` action

```js
ayakashi
    .select("inPageLink")
    .where({id: {eq: "theLink"}});
await ayakashi.spaNavigationClick("inPageLink");
```

`spaNavigationClick()` needs a [prop](/docs/guide/tour.html#props) to click.  
It will click the prop and then wait for some kind of navigation to happen in the page itself (without a reload).  
There is an optional timeout you can specify (in ms) as a second parameter (default is 10s).  
You may use a timeout of `0` to disable the timeout.

## Using the raw events

Sometimes `goTo()`, `navigationClick()` or `spaNavigationClick()` might not be enough if the page we are trying
to scrape is using a different navigation scenario.  
For these cases we can wait for the navigation events directly

```js
//for standard navigation (load event)
await ayakashi.waitForLoadEvent();
//for standard navigation (DOMContentLoaded event)
await ayakashi.waitForDomContentLoadedEvent();
//for spa navigation
await ayakashi.waitForInPageNavigation();
```

You will most probably want to combine the events with some kind of page interaction.  
Let's illustrate that by re-implementing a version of `navigationClick()` and `spaNavigationClick()`

```js
ayakashi
    .select("myLink")
    .where({id: {eq: "theLink"}});
await Promise.all([
    ayakashi.click("myLink"),
    ayakashi.waitForLoadEvent()
]);
```

```js
ayakashi
    .select("inPageLink")
    .where({id: {eq: "theLink"}});
await Promise.all([
    ayakashi.click("inPageLink"),
    ayakashi.waitForInPageNavigation()
]);
```