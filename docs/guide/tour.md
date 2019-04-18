---
layout: default
title: Tour
parent: Guide
nav_order: 2
---

<!-- markdownlint-disable MD022 -->
# Ayakashi tour
{: .no_toc }
<!-- markdownlint-enable MD022 -->

Welcome aboard!  
In this section we will cover all of Ayakashi's basic building blocks and
how they all fit together to create complete and ambitious scraping systems.  
We will start with **scrappers** and the different components that comprise a scrapper,
then move to **scripts** and finally see how we use the two in a **pipeline**.

* scrappers
  * props
  * extractors
  * actions
  * preloaders
* scripts
* pipelines
{:toc}

## scrappers

If you have already read how to [run a simple scrapper](/docs/guide/running-a-simple-scrapper.html)
you already know kind of how scrappers look like.  
Inside scrappers we place our page interaction logic, like navigation
and interacting with page elements (clicking things, filling forms etc).  
One more thing we want to do in a page is to **extract** data from it, we accomplice this
inside a scrapper as well.

### props

Ayakashi's way of finding things in the page and using them is
done with props and domQL.  
Directly inspired by the relational database world (and SQL), domQL makes
DOM access easy and readable no matter how obscure the page's structure is.  
Props are the way to package domQL expressions as re-usable structures which
can then be passed around to actions or to be used as models for data
extraction.

They are defined by giving them a name and a domQL query for how to find them in the page:

```js
    ayakashi
        .selectOne("myButton")
        .where({
            and: [{
                class: {
                    eq: "btn"
                }
            }, {
                "style-background-color": {
                    eq: "rgb(40, 167, 69)"
                }
            }, {
                textContent: {
                    like: "Click me"
                }
            }]
        });
```

We can then use it by referencing its name (`myButton`).  
Props are a vital component since they are used as both action input and for data extraction.  
Make sure to read the [domQL section](/docs/guide/querying-with-domql.html)
for a complete reference on how to define and query them.

### extractors

Extractors are used to extract data from a prop.  
Consider the following element and then a prop for that element:

```html
<div id="myDiv">hello</div>
```

```js
ayakashi.select("myDivProp").where({id: {eq: "myDiv"}});
```

we can extract its text by using the `text` extractor, like this:

```js
const result = await ayakashi.extract("myDivProp", "text");
```

This is a very basic example and there is quite more to read about extractors on the
[data extraction section](/docs/guide/data-extraction.html).

### actions

Actions are high level functions that are used to interact with a page. For example:

```js
await ayakashi.click("myButtonProp");
```

```js
await ayakashi.waitUntilExists("searchBox");
await ayakashi.typeIn("searchBox", "some text to search");
```

You can read about all of the builtin actions in the [reference](/reference/builtin-actions.html).
as well as how to [create your own](/docs/advanced/creating-your-own-actions.html).

### preloaders

Preloaders are used to load a piece of code and make it available in a page that will be loaded by a scrapper.  
Both [3rd party libraries](/docs/going_deeper/loading-libraries-as-preloaders.html)
as well as [any other code](/docs/advanced/creating-your-own-preloaders.html)
you wish can be preloaded and made available (or even executed) before any of the page's code has begun loading.

## scripts

Scripts are really simple, they are just functions that complement our scrappers.  
For example a script could:

* cleanup/normalize the data we just extracted
* hit an external API to enrich our data
* save our data

The [builtin saving methods](/docs/guide/builtin-saving-scripts.html) (sql, json, csv)
are actually implemented as scripts.  
One thing to note is that inside scripts there is no page or browser access, they are meant to be run before and/or after
scrappers (which of course have page access) in a standalone manner to keep things structured and readable.  
We will see an example of how to create a script when we build our first [complete project](/docs/guide/building-a-complete-scraping-project.html).

## pipelines

With pipelines we can integrate scrappers and scripts together.  
They are defined inside an `ayakashi.config.js` file which is the file that describes
and configures a complete ayakashi project.  
We will see a complete example in the next section where we will build a [complete project](/docs/guide/building-a-complete-scraping-project.html),
but they are pretty simple and they look like this:

```js
{
    waterfall: [{
        type: "scrapper",
        module: "myScrapper"
    }, {
        type: "script",
        module: "printToConsole"
    }]
}
```

This instructs ayakashi to first run our scrapper `myScrapper` and then pass the extracted data
to our `printToConsole` script which will just print our data.  
The `waterfall` part means: "run this in a serial (waterfall) manner, one after the other, while passing data
from one to the next".  
Pipelines are also used to parallelize our tasks, as they also have a `parallel` mode in conjunction to `waterfall`, as
well as the ability to mix and match them together to create many interest scenarios.
