---
layout: default
title: Debugging
parent: Guide
nav_order: 6
---

# Debugging

Ayakashi includes some utilities to help you debug your scrapers while developing.

## Using a headful chrome

Ayakashi will run a chromium instance in headless mode by default.  
You can change this in the top-level `config` block in your `ayakashi.config.js` file:

```js
module.exports = {
    config: {
        headless: false
    },
    //... the rest of the file
};
```

Another little helper to use when going headful mode is to automatically open the devTools for each new tab:

```js
module.exports = {
    config: {
        headless: false,
        openDevTools: true
    },
    //... the rest of the file
};
```

## Pausing execution

Inside a **scraper file** you can add:

```js
await ayakashi.pause();
```

to pause the execution of the scraper at that point.  
To resume it, you should be running the scraper in **non-headless** mode.  
Go to the tab that the scraper you paused is managing and in the **devTools' console** run:

```js
ayakashi.resume();
```

**Note**: the `pause` method is only available inside scrapers.