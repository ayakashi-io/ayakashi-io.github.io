---
layout: default
title: Manipulating Cookies
parent: Going Deeper
nav_order: 12
---

<!-- markdownlint-disable MD022 -->
# Manipulating Cookies
{: .no_toc }
<!-- markdownlint-enable MD022 -->

<!-- markdownlint-disable MD022 -->
## Table of contents
{: .no_toc .text-delta }
<!-- markdownlint-enable MD022 -->

* Automatic cookie sharing
* Manual cookie manipulation
  * Setting cookies
  * Getting cookies
* Sharing cookies between multiple pipeline steps
{:toc}

## Automatic cookie sharing

In each scraper([normal](/docs/guide/building-a-complete-scraping-project.html), [renderless](/docs/guide/renderless-scrapers.html) or [apiScraper](/docs/guide/api-scrapers.html)) step, cookies are automatically shared
between all page loads (renderless and normal) and http requests.  
For example:  

```js
await ayakashi.goTo("https://somepage.com");
//...later
const apiResponse = await ayakashi.get("https://somepage.com/api");
```

or in a renderless scraper: 

```js
await ayakashi.load("https://somepage.com");
//...later
const apiResponse = await ayakashi.get("https://somepage.com/api");
```

The API call above will include all cookies we got from the initial page load.  
Can be pretty useful for pages that have some kind of protection on their internal APIs, eg. some session ID which you get if you load the full page or an authentication token after a login.

## Manual cookie manipulation

Ayakashi also includes a set of APIs to manage cookies manually.

### Setting cookies

```js
await ayakashi.setCookie({
    key: "myCookie",
    value: "test",
    domain: "somepage.com",
    path: "/",
    expires: "2029-10-25T17:29:23.375Z", //optional
    secure: true, //optional
    httpOnly: true, //optional
    hostOnly: false //optional
});
```
A `setCookies()` method is also available to set multiple cookies at once and accepts an array of cookie options.  
The automatic sharing described above also applies to cookies set manually.

### Getting cookies

```js
const cookie = await ayakashi.getCookie({ //filter object
    key: "myKey" //optional,
    domain: "somepage.com" //optional,
    path: "/test" //optional,
    url: "https://somepage.com" //optional
});
```
All keys in the filter object are optional. Use them in any combination to get the cookie you want.  
A `getCookies()` method is also available to get multiple cookies that pass the filter at once.

## Sharing cookies between multiple pipeline steps

The sharing happening above is only valid inside a single scraper pipeline step.  
If you need to share cookies between multiple pipeline steps (and scraper types) you can either
enable [persistentSession](/docs/going_deeper/persisting-sessions.html) or use the above cookie APIs like this:

```js
//inside a normal scraper
module.exports = async function(ayakashi, input, params) {
    //load a page
    await ayakashi.goTo("https://somepage.com");
    //get all the cookies
    const cookies = await ayakashi.getCookies();
    //return them as the step output
    return {cookies: cookies};
};
```

```js
//then in an apiScraper
module.exports = async function(ayakashi, input, params) {
    //set the cookies we got from the input
    await ayakashi.setCookies(input.cookies);
    //hit your API
    const apiResponse = await ayakashi.get("https://somepage.com/api");
};
```

