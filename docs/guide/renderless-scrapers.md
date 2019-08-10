---
layout: default
title: Renderless scrapers
parent: Guide
nav_order: 7
---

<!-- markdownlint-disable MD022 -->
# Renderless Scrapers
{: .no_toc }
<!-- markdownlint-enable MD022 -->

Apart from the standard headless-chrome based scrapers, Ayakashi also supports renderless scrapers.  
Renderless scrapers are like classic html parsing scrapers and do not run any javascript or render dynamic pages.  
They have some super powers though, like full access to [domQL](/docs/guide/querying-with-domql.html)
and the [extraction API](/docs/guide/data-extraction.html).  
<br/>
Renderless scrapers are perfect for pages that are either not dynamic or somehow pre-rendered on the server and can
be fetched in their entirety in a single request.  
They are also a magnitude faster than standard scrapers since they don't have to load any resources or run any javascript.  
<br/>
So, before reaching for a standard scraper, take a look in the page's source (with view-source) to see if everything you need
is there. If the html contains everything you need and you also don't need to perform any dynamic page manipulation
(like clicking things, scrolling etc) then renderless scrapers can be the perfect (and much faster!) alternative.

<!-- markdownlint-disable MD022 -->
## Table of contents
{: .no_toc .text-delta }
<!-- markdownlint-enable MD022 -->

* Generating and running a renderless scraper
* What's available in a renderless scraper
* A note about style domQL queries
{:toc}

## Generating and running a renderless scraper

You can generate a renderless scraper by running the following command either in a new project or an existing project:

```bash
ayakashi new --renderlessScraper --name=myScraper
```

The following file will be generated:

```js
/**
* @param {import("@ayakashi/types").IRenderlessAyakashiInstance} ayakashi
*/
module.exports = async function(ayakashi, input, params) {
    await ayakashi.load(input.page);
    ayakashi
        .select("about")
        .where({itemprop: {eq: "about"}});

    return ayakashi.extract("about");
};
```

You will notice the code is pretty much the same as the normal scrapers we built in previous sections.  
The only difference is that instead of the [.goTo()](/docs/reference/builtin-actions.html#goTo) method we use
the `load()` method.  
<br/>
Then let's register our renderless scraper in our config:

```js
/**
* @type {import("@ayakashi/types").Config}
*/
module.exports = {
    config: {},
    waterfall: [{
        type: "script",
        module: "getPage"
    }, {
        type: "renderlessScraper",
        module: "myScraper"
    }, {
        type: "script",
        module: "printToConsole"
    }]
};
```

The only difference here, is that renderless scrapers have a type of `renderlessScraper` instead of `scraper` in our pipeline.  
<br/>
And then run it as normal:  

```bash
ayakashi run
```

## What's available in a renderless scraper

Renderless scrapers have full access to [domQL](/docs/guide/querying-with-domql.html) and can
use the [extraction API](/docs/guide/data-extraction.html) as normal.  
They also support [retries](/docs/going_deeper/automatic_retries.html) and the `yield()` methods.  
Basically, the only thing they can't do is use the dynamic [core actions](/docs/reference/builtin-actions.html)
available in normal scrapers.

## A note about style domQL queries

One final caveat of renderless scrapers is that they won't be able to find matches with [domQL style queries](/docs/guide/querying-with-domql.html#querying-with-style) if the style rule is defined in
an external stylesheet (since external resources are not available).  
Inline styles or styles defined in `<style>` tags inside the document will work fine.
