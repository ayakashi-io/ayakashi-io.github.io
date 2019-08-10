---
layout: default
title: Yielding data
parent: Going Deeper
nav_order: 11
---

# Yielding data

Ayakashi provides multiple ways to yield data from one [pipeline](/docs/guide/tour.html#pipelines) step to another so it can be
as flexible as possible and support many scenarios.

## Yielding data from scrapers

### standard return

The first choice is to just use a javascript `return` statement. Anything you return will be passed as input to the next step of the pipeline.  

```js
ayakashi.select("myDivProp").where({id: {eq: "myDiv"}});
const result = await ayakashi.extract("myDivProp");
return result;
```

Ayakashi also includes some higher level APIs so we can better control the data flow.

### yield()

`yield()` is like a `return` but it can be used any number of times like in a loop or yield something conditionally and then continuing with the rest of the scraper.

```js
ayakashi.select("myDivProp").where({id: {eq: "myDiv"}});
const result = await ayakashi.extract("myDivProp");
await ayakashi.yield(result);
//keep doing work
```

### yieldEach()

Yields multiple extractions individually in a single (atomic) operation.  
The next step of the pipeline will run for each extraction.

```js
await ayakashi.yieldEach(extractedLinks);
//is kinda like this
for (const link of extractedLinks) {
    await ayakashi.yield(link);
}
//but ensures the yields are performed as a single unit
```

### recursiveYield()

Recursively re-run the scraper by yielding the extracted data to itself.
The data will be available in the input object.  
`recursiveYield()` is perfect for pagination. We can keep yielding the next page to ourself while using a `yield()` to pass the page data to
the next step.

```js
module.exports = async function(input) {
    const currentPage = input.currentPage || 0;
    await ayakashi.goTo(`https://example.com?page=${currentPage}`);

    //extract and yield() some data
    ayakashi.select("myDivProp").where({id: {eq: "myDiv"}});
    const result = await ayakashi.extract("myDivProp");
    await ayakashi.yield(result);

    //repeat
    await ayakashi.recursiveYield({currentPage: currentPage += 1});
};
```

### recursiveYieldEach()

Recursively re-run the scraper by yielding multiple extractions individually in a single (atomic) operation.  
Like `recursiveYield()` but when we need to yield multiple things to ourselves.

## Yielding data from scripts

Scripts don't include any extra APIs. They are kept as simple as possible so they can be versatile to use in any type of workload.  
To yield data from a script we can just use a standard javascript `return` statement or return a [generator](/docs/going_deeper/script-generators.html).