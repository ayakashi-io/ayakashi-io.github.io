---
layout: default
title: Loading 3rd party libraries as preloaders
parent: Going Deeper
nav_order: 2
---

# Loading 3rd party libraries as preloaders

Ayakashi allows any 3rd party package (that can be used in the browser) to be loaded and be available in the page
for your scrapers to use.  
<br/>
**Note**: Preloaders are used to load extra code in the **actual browser page**, so you can then use it
inside [evaluate()](/docs/going_deeper/evaluating-javascript-expressions.html) calls.  
If you need any kind of library in your scrapers or scripts just install it and `require()` it as you normally would.

## Example (lodash)

Let's make [lodash](https://lodash.com/), a very popular support library, available in our loaded pages.  
First we need to install it in our project.  
Inside the project folder, run:

```bash
npm install --save lodash
```

Then let's load it in our scraper.  
Inside the project's `ayakashi.config.js`:

```js
module.exports = {
    //... the rest of the file
    "waterfall": [{
        "type": "scraper",
        "module": "myScraper",
        "load": {
            "preloaders": [{
                module: "lodash",
                as: "_"
            }]
        }
    }]
    //... the rest of the file
};
```

Use the same name of the package as the one you installed (`lodash` in our case) under `module`.  
You can also alias it using `as` (optional).  
<br/>
The package is then available to be used inside [evaluate()](/docs/going_deeper/evaluating-javascript-expressions.html) calls:

```js
await ayakashi.evaluate(function() {
    console.log(ayakashi.preloaders._.VERSION);
});
```

## Another Example (Jquery)

Let's also see how to use [jQuery](https://jquery.com/) just to illustrate the extra option `waitForDOM`.  

```bash
npm install --save jquery
```

```js
module.exports = {
    //... the rest of the file
    "waterfall": [{
        "type": "scraper",
        "module": "myScraper",
        "load": {
            "preloaders": [{
                module: "jquery",
                as: "$",
                waitForDOM: true
            }]
        }
    }]
    //... the rest of the file
};
```

`Jquery` (and many other libraries) need the page to be fully loaded before they can run.  
The `waitForDOM` option will do exactly that, it will only load the preloader when the page's DOM is fully loaded.  
It's like placing the `<script>` tag at the end of the `<body>`.

## Your own libraries as preloaders

You can of course load any local library that you have written as a preloader.  
You can learn how [here](/docs/advanced/creating-your-own-preloaders.html).
