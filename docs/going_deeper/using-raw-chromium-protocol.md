---
layout: default
title: Using the raw chromium protocol
parent: Going Deeper
nav_order: 13
---

# Using the raw chromium protocol

The chromium protocol includes a plethora of APIs that allows us to control almost every function of the browser.  
Ayakashi wraps a lot of this functionality in higher level fully typed APIs but you might find yourself missing something or needing a bit more power.  
The protocol API documentation can be found here: [https://chromedevtools.github.io/devtools-protocol/](https://chromedevtools.github.io/devtools-protocol/)

## Some examples

You can access the protocol client on `ayakashi.__connection.client`.  
It's a good idea to wrap them in [custom actions](/docs/advanced/creating-your-own-actions.html) to make them reusable and keep your scrapers clean.  
**Note**: The client is only available on normal scrapers (not on renderless, apiScrapers or scripts).  
**Note**: The client API is not typed. You will have to check the protocol docs for parameters and responses.

### Taking screenshots

```javascript
const fs = require("fs");
/**
* @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
*/
module.exports = async function(ayakashi, input, params) {
    await ayakashi.goTo("https://github.com/ayakashi-io/ayakashi");

    //wait a bit for everything to be rendered correctly on the page
    await ayakashi.wait(5000);

    //get the actual page dimensions so we can take a full page screenshot
    const metrics = await ayakashi.__connection.client.Page.getLayoutMetrics();

    //take the screenshot
    //check https://chromedevtools.github.io/devtools-protocol/tot/Page/#method-captureScreenshot for more options
    const img = await ayakashi.__connection.client.Page.captureScreenshot({
        format: "png",
        clip: {
            x: 0,
            y: 0,
            width: metrics.cssContentSize.width,
            height: metrics.cssContentSize.height,
            scale: 1
        },
        captureBeyondViewport: true
    });

    //save it to a file
    fs.writeFileSync("./img.png", img.data, {encoding: "base64"});
};
```

### Auto-accept browser alerts

For some APIs you need to register a listener.  
In these cases we need some extra work to let ayakashi know how to clean them up after we are done.  

```javascript
/**
* @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
*/
module.exports = async function(ayakashi, input, params) {
    //register our listener
    const unsubscribe = ayakashi.__connection.client.Page.javascriptDialogOpening(async function(data) {
        //auto-accept the dialog when it pops
        console.log("auto-accepting alert:", data);
        await ayakashi.__connection.client.Page.handleJavaScriptDialog({accept: true});
    });
    //let ayakashi know about our new listener
    ayakashi.__connection.unsubscribers.push(unsubscribe);

    //...rest of the scraper file omitted
    //load page etc
};
```

### Track headers (wrapped in an action)

Check the documentation [here](/docs/advanced/creating-your-own-actions.html) on how to generate custom actions.  
We will create an action that tracks all header pairs for the page we are going to load (you can remove the domain check if you want to include headers from 3rd party resources).  
  
Inside the action file:

```javascript
const URL = require("url").URL;
/**
* @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
*/
module.exports = async function(ayakashi) {
    ayakashi.registerAction("trackDomainHeaders", async function(domain) {
        const headers = [];

        const unsubscribe = ayakashi.__connection.client.Network.responseReceived(function(data) {
            const incomingUrl = new URL(data.response.url);
            if (incomingUrl.hostname.includes(domain)) {
                headers.push(data.response.headers);
            }
        });
        //clean up
        ayakashi.__connection.unsubscribers.push(unsubscribe);

        //return a closure to read the headers after page load
        return function readHeaders() {
            return headers;
        };
    });
};
```

On our scraper:

```javascript
/**
* @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
*/
module.exports = async function(ayakashi, input, params) {
    //run our action and get back a function we can call after page load to get the headers
    const readHeaders = await ayakashi.trackDomainHeaders("some-domain.com");
    //load the page
    await ayakashi.goTo("https://some-domain.com");
    //wait a bit so async resources get loaded and we track every header
    await ayakashi.wait(5000);

    //get the headers and extract whatever we need
    const headers = readHeaders();
    console.log(headers);
};
```