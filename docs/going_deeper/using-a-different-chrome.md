---
layout: default
title: Using an alternative chrome/chromium
parent: Going Deeper
nav_order: 4
---

# Using an alternative chrome/chromium

By default, Ayakashi will download and use the latest chromium revision available.  
You can overwrite this and use any **installed** chrome/chromium executable you wish.  
Just set the **executable's** path in your project's `ayakashi.config.js`:

```js
module.exports = {
    config: {
        chromePath: "/usr/bin/chromium-browser"
    },
    //... the rest of the file
}
```