---
layout: default
title: Debugging
parent: Guide
nav_order: 6
---

# Debugging

Ayakashi includes some utilities to help you debug your scrappers while developing.

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

Inside a **scrapper file** you can add:

```js
await ayakashi.pause();
```

to pause the execution of the scrapper at that point.  
To resume it, you should be running the scrapper in **non-headless** mode.  
Go to the tab that the scrapper you paused is managing and in the **devTools' console** run:

```js
ayakashi.resume();
```

**Note**: the `pause` method is only available inside scrappers.