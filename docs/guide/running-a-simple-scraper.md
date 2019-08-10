---
layout: default
title: Running a simple scraper
parent: Guide
nav_order: 1
---

# Running a simple scraper

This is the very first section where we can start getting our feet wet by looking
at and running some code.  
If you would like to play along, head over to [installation](/docs/installation)
first to get ayakashi in your system.  

We will make a pretty simple scraper that loads a github repo,
clicks a button and extracts some data like the clone url and the star count.  

## The scraper file

We will place all of our code in a single file to keep things simple.  
Here is the complete code:

```js
module.exports = async function(ayakashi) {
    //go to the page
    await ayakashi.goTo("https://github.com/ayakashi-io/ayakashi");

    //find and extract the about message
    ayakashi
        .selectOne("about")
        .where({itemprop: {eq: "about"}});
    const about = await ayakashi.extract("about", "text");

    //find and extract star count
    ayakashi
        .selectOne("stars")
        .where({href: {like: "/stargazers"}});
    const stars = await ayakashi.extract("stars", "number");

    //find the green button that opens the clone dialog
    ayakashi
        .selectOne("cloneDialogTrigger")
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
                    like: "Clone"
                }
            }]
        });
    //click it
    await ayakashi.click("cloneDialogTrigger");

    //find and extract the clone url
    ayakashi
        .selectOne("cloneUrl")
        .where({"aria-label": {like: "Clone this repository at"}});
    const cloneUrl = await ayakashi.extract("cloneUrl", "value");

    //return our results
    return {about, stars, cloneUrl};
};
```

As you can see, a scraper is a [nodejs](https://nodejs.org) module that exports an
[async](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) function
(so we can use the convenient nice `async/await` syntax).  

The scraper does the following things in a serial manner:

* Loads a repository page
* Creates the `about` prop and then extracts its value as text
* Creates the `stars` prop and then extracts its value as a number
* Creates the `cloneDialogTrigger` prop which points to the Clone button
* Clicks the `cloneDialogTrigger` prop
* Creates another prop, called `cloneUrl` and extracts it
* Returns the results

The prop construct is used to
define all the different entities in the page by giving them a name and how to find them.  
They can then be used for extraction or passed to actions (like `click` above).  
We will explore them in more detail in the [tour section](/docs/guide/tour.html#props) and learn
everything about the syntax in the [domQL section](/docs/guide/querying-with-domql.html).  
Extracting data is also fully covered in the [data extraction section](/docs/guide/data-extraction.html).

## Let's run it

```bash
ayakashi run --simple ./github.js
```

The `run` command will download a recommended chromium version the first time it is run.  
It will then start outputting some info about its progress and finally print a table on our console
with the extracted data.

## Simple mode (`--simple`)

On our `run` command, the `--simple` flag is used to enable simple mode.  
With simple mode we can run single file scrapers directly.  
It's a valuable tool for quick prototyping, simple scrapers or examples.  
By default the `run` command is looking for an Ayakashi project folder, which is what we will
build on the [complete scraper project](/docs/guide/building-a-complete-scraping-project.html) section.

## Next steps

This section served as an introduction to get a bit familiar with the base API
and the `run` command.  
Next follows the [tour](/docs/guide/tour.html) in which we will get in more detail for each concept
and then move on to build a [complete project](/docs/guide/building-a-complete-scraping-project.html)
by re-using the scraper of this section while enhancing it.