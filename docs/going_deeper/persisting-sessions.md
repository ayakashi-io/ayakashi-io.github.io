---
layout: default
title: Persistent Sessions
parent: Going Deeper
nav_order: 3
---
# Persistent Sessions

## How sessions work

Ayakashi by default uses a different session for each pipeline step.  
Sessions are little containers of data and settings and do not leak to the rest of
the pipeline.

## Session data

Each session includes the following:

* a chromium context (with its cookies, localstorage, caches etc)
* a [cookie](/docs/going_deeper/manipulating-cookies.html) jar
* scraper settings (like userAgents and other [options](/docs/reference/ayakashi-config-file.html#emulatoroptions))

## Using a persistent session

Sometimes, sharing these values across the whole pipeline is useful.  
You can enable this option by setting the `persistentSession` flag in your project's `ayakashi.config.js` file:

```js
module.exports = {
    config: {
        persistentSession: true
    },
    //... the rest of the file
}
```

## Shared data

If `persistentSession` is enabled the following data will be shared:

* the userAgent and other platform settings will be shared and be the
same in every pipeline step.
* all cookies will be synced to every step. All page loads and http requests will
use the same set of cookies.
* chromium will not use a new context for each step (like what happens in a standard non-incognito user session)