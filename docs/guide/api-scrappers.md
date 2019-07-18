---
layout: default
title: API scrappers
parent: Guide
nav_order: 8
---

<!-- markdownlint-disable MD022 -->
# API Scrappers
{: .no_toc }
<!-- markdownlint-enable MD022 -->

If there is an API available, the smart thing is to use it.  
That's why Ayakashi comes bundled with API scrappers which are basically http request clients
with extra features sprinkled on top so you can plug them on your Ayakashi pipelines.

<!-- markdownlint-disable MD022 -->
## Table of contents
{: .no_toc .text-delta }
<!-- markdownlint-enable MD022 -->

* Generating and running an API scrapper
* What's available in an API scrapper
* Request options
{:toc}

## Generating and running an API scrapper

You can generate an API scrapper by running the following command either in a new project or an existing project:

```bash
ayakashi new --apiScrapper --name=myScrapper
```

The following file will be generated:

```js
/**
* @param {import("@ayakashi/types").IApiAyakashiInstance} ayakashi
*/
module.exports = async function(ayakashi, input, params) {
    const manifest = await ayakashi.get("https://ayakashi.io/manifest.json");
    console.log("Latest Ayakashi version:", manifest.ayakashi.version);
};
```

Then let's register our API scrapper in our config:

```js
/**
* @type {import("@ayakashi/types").Config}
*/
module.exports = {
    config: {},
    waterfall: [{
        type: "apiScrapper",
        module: "myScrapper"
    }]
};
```

The only difference in their config is their type, which must be `apiScrapper`.

## What's available in an API scrapper

There are methods for all available http verbs:

* get()
* post()
* patch()
* put()
* delete()
* head()

All methods accept a url string and an optional configuration object (check options below).  
They return a promise with the request's response body.

API scrappers also support [retries](/docs/going_deeper/automatic_retries.html) and the `yield()` methods.  
Proxy settings, user agents, session configuration, standard headers etc are handled automatically.

## Request options

Under the hood, API scrappers use a modified version of the popular [request.js](https://github.com/request/request) package.  
Feel free to use any of its [options](https://github.com/request/request#requestoptions-callback) that fit your usecase
but bear in mind that many options are already pre-configured.  
The configuration object should be mostly used to pass request data like querystrings, json payloads etc.
