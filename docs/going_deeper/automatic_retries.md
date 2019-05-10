---
layout: default
title: Automatic retries
parent: Going Deeper
nav_order: 9
---

# Automatic retries

Doing any work that involves the network or external resources can be unpredictable and error-prone, that's
why Ayakashi includes some retrying mechanisms to make our scrappers more robust.

## Retrying distinct operations

You can retry a single operation (or a set of operations) using the `ayakashi.retry()` API:  

```js
ayakashi
    .select("myLink")
    .where({id: {eq: "theLink"}});

await ayakashi.retry(async function() {
    await ayakashi.navigationClick("myLink");
}, 5);
```

If the loading of the page `navigationClick()` tries to load fail for any reason (timeout etc), it will then be retried 5 times,
in increasing intervals.  
In general, any error throwed (or a rejected promise returned) inside the retry block will trigger
the retry so it can be used for any kind of operation that has a chance to fail.  

* The function passed to retry must be asynchronous (return a Promise) or an error will be raised.  
* Any result returned by the passed function will also be returned by retry.  
* All errors raised inside retry will be printed to the console.  
* After all retries have been exhausted, the error will finally be raised.  
* `retry()` uses **10** retries by default but can be configured.

## Retrying a whole pipeline step

A whole pipeline step (scrapper or script) can also be retried by specifying a `retries` key in
`ayakashi.config.js` for that step:

```js
module.exports = {
    config: {},
    waterfall: [{
        type: "script",
        module: "myScript"
    }, {
        type: "scrapper",
        module: "myScrapper",
        config: {
            retries: 10
        }
    }, {
        type: "scrapper",
        module: "anotherScrapper"
    }]
};
```

If `myScrapper` raises any unhandled error, it will then be retried for 10 times by reloading the whole
step each time (so it will start again from the very beginning of the step).  
If it doesn't succeed after 10 times, the step will be aborted and the pipeline execution
will be halted (`anotherScrapper` won't run).  
By default **no retries** are performed in any step.