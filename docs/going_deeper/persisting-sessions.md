---
layout: default
title: Using a persistent session
parent: Going Deeper
nav_order: 3
---

# Using a persistent session

By default, Ayakashi will create and use a new session every time it runs.  
If you need to use the same session for many runs (to keep cookies etc) you can use
the `persistentSession` config flag.  
You can set it in your `ayakashi.config.js` file

```js
module.exports = {
    config: {
        persistentSession: true
    },
    //... the rest of the file
}
```

This will create a `.session` folder inside your project's folder with the session's data.  
You can just delete it if you need to refresh the session.