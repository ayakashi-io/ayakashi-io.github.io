---
layout: default
title: API scrapers
parent: Guide
nav_order: 8
---

<!-- markdownlint-disable MD022 -->
# API Scrapers
{: .no_toc }
<!-- markdownlint-enable MD022 -->

If there is an API available, the smart thing is to use it.  
That's why Ayakashi comes bundled with API scrapers which are basically http request clients
with extra features sprinkled on top so you can plug them on your Ayakashi pipelines.

<!-- markdownlint-disable MD022 -->
## Table of contents
{: .no_toc .text-delta }
<!-- markdownlint-enable MD022 -->

* Generating and running an API scraper
* What's available in an API scraper
* Request options
* The request API is available on all scraper types
{:toc}

## Generating and running an API scraper

You can generate an API scraper by running the following command either in a new project or an existing project:

```bash
ayakashi new --apiScraper --name=myScraper
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

Then let's register our API scraper in our config:

```js
/**
* @type {import("@ayakashi/types").Config}
*/
module.exports = {
    config: {},
    waterfall: [{
        type: "apiScraper",
        module: "myScraper"
    }]
};
```

The only difference in their config is their type, which must be `apiScraper`.

## What's available in an API scraper

There are methods for all available http verbs:

* get()
* post()
* patch()
* put()
* delete()
* head()

All methods accept a url string and an optional configuration object (check options below).  
They return a promise with the request's response body.

API scrapers also support [retries](/docs/going_deeper/automatic_retries.html) and the [yield](/docs/going_deeper/yielding-data.html) methods.  
Proxy settings, user agents, session configuration, standard headers etc are handled automatically.

## Request options

Under the hood, API scrapers use a modified version of the popular [request.js](https://github.com/request/request) package.  
Feel free to use any of its [options](https://github.com/request/request#requestoptions-callback) that fit your usecase
but bear in mind that many options are already pre-configured.  
The configuration object should be mostly used to pass request data like querystrings, json payloads etc.

## The request API is available on all scraper types

All scraper types (`renderlessScraper`/`scraper`) have access to the request API to make them flexible on
scenarios that require both an API call and some form of DOM manipulation.  
If you only need to make API requests, an `apiScraper` is recommended since it's more lightweight and
makes the code more explicit.
