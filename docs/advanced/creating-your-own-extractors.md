---
layout: default
title: Creating your own extractors
parent: Advanced
nav_order: 1
---

# Creating your own extractors

Advanced
{: .label .label-red }

This is an advanced section on how to create your own extractors, either from scratch or by re-using and depending upon existing
extractors (both builtin and custom made).  
If you haven't done so already, you may want to read [the extractor tour](/docs/guide/tour.html#extractors)
and the [data extraction](/docs/guide/data-extraction.html) section.

## Creating an extractor from scratch (a simple `id` extractor)

Ayakashi includes a command to generate a new extractor, let's use it.  
Inside your project's root folder, run:

```bash
ayakashi new --extractor --name=id
```

This will create an `extractors` folder and our `id.js` extractor inside it.  
`id.js` contains some example content which will suffice for our example

```js
/**
 * @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
 */
module.exports = function(ayakashi) {
    ayakashi.registerExtractor("id", function() {
        return {
            extract: function(element) {
                return element.id;
            },
            isValid: function(result) {
                return !!result;
            },
            useDefault: function() {
                return "no-id-found";
            }
        };
    });
};
```

As you might have already figured, this is an extractor that extracts an element's `id`.  
An `id` extractor is not something you would normally create, since the [extraction](/docs/guide/data-extraction.html) API
is perfectly capable to handle it without any extra code but it is really simple and serves as a good example.  
<br/>
New extractors should always be placed inside the `extractors` folder of a project.  
`registerExtractor()` is used to create our new extractor. It accepts a function that should return an object with
the following methods:

* `extract()`
* `isValid()`
* `useDefault()`

When our extractor is used and a prop is passed to it, each of the prop's element matches will
be passed to the `extract()` method one by one.  
Inside `extract()` we should return whatever it is we need from the element, in our case we return the element's `id`.  
<br/>
Then the `isValid()` method is called with the result that `extract()` returned.  
Inside `isValid()` we should check if the extraction is an acceptable value and return `true` or `false`.  
In our example case we just check that an id was found.  
<br/>
If our `isValid()` method returned `false`, the `useDefault()` method will be called, in which we should
return a default value we would like to use.

## Re-using existing extractors (`email` extractor)

Let's create something more complete and useful this time.  
An `email` extractor which will extract an email address from a prop's text.  
<br/>
We will see how to re-use existing extractors and built upon them (the `text` extractor in our case).  
<br/>
We will use an email regex taken from [emailregex.com](http://emailregex.com/), which should work fine in most cases.  
<br/>
Let's generate our new extractor:

```bash
ayakashi new --extractor --name=email
```

This time we will replace the generated example content with:

```js
/**
 * @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
 */
module.exports = function(ayakashi) {
    ayakashi.registerExtractor("email", function() {
        return {
            extract: function(element) {
                //let's use the text extractor
                const textExtractor = ayakashi.extractors.text();
                const textResult = textExtractor.extract(element);
                //if no text was found at all, return null
                if (!textExtractor.isValid(textResult)) {
                    return null;
                }
                //let's check our email regex
                const EMAIL_REGEX = /(?:[a-z0-9!#$%&'*+/=?^_`{|}~-]+(?:\.[a-z0-9!#$%&'*+/=?^_`{|}~-]+)*|"(?:[\x01-\x08\x0b\x0c\x0e-\x1f\x21\x23-\x5b\x5d-\x7f]|\\[\x01-\x09\x0b\x0c\x0e-\x7f])*")@(?:(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?|\[(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?|[a-z0-9-]*[a-z0-9]:(?:[\x01-\x08\x0b\x0c\x0e-\x1f\x21-\x5a\x53-\x7f]|\\[\x01-\x09\x0b\x0c\x0e-\x7f])+)\])/;
                const match = textResult.match(EMAIL_REGEX);
                if (match) {
                    return match.join("");
                } else {
                    return null;
                }
            },
            isValid: function(result) {
                return !!result;
            },
            useDefault: function() {
                return "no-email-found";
            }
        };
    }, ["text"]);
};
```

Ok, let's see what's going on here.  
<br/>
Notice the extra parameter `["text"]` after the function. This is how we let Ayakashi know that we depend upon
the `text` extractor. Any number of extractors can be passed here.  
<br/>
First we initialize the `text()` extractor and try to get the element's text.
If no text was found at all we return `null`.  
Then we match the text result against our regex, returning it if it matched or returning null otherwise.  
<br/>
Our `isValid()` method is pretty simple, it just checks if we returned a truthy value.  
<br/>
Our `useDefault()` method will just return the string `"no-email-found"` if no email was found.
