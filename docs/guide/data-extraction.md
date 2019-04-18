---
layout: default
title: Data extraction
parent: Guide
nav_order: 5
---

<!-- markdownlint-disable MD022 -->
# Data extraction
{: .no_toc }
<!-- markdownlint-enable MD022 -->

In this section we will see all the different cases of how `extract` can be used to get the data
we want out of a [prop](/docs/guide/tour.html#props).
If you haven't read it already, take a minute to check the short [tour](/docs/guide/tour.html).

<!-- markdownlint-disable MD022 -->
## Table of contents
{: .no_toc .text-delta }
<!-- markdownlint-enable MD022 -->

* Text extraction
* Attribute extraction
* Regex extraction
* Extracting multiple values
* Using defaults
* Using extraction functions
* Other builtin extractors
* Creating your own extractors
{:toc}

## Text extraction

The simplest possible extraction

```js
const result = await ayakashi.extract("myProp", "text");
```

If no extractor is specified, the `text` extractor is used.  
So this is equivalent

```js
const result = await ayakashi.extract("myProp");
```

The extraction result will be an array of objects with key the prop's
name and value our extraction.  
So for the following

```html
<div id="myDiv">hello</div>
```

The result will be this

```js
ayakashi.select("myDivProp").where({id: {eq: "myDiv"}});
const result = await ayakashi.extract("myDivProp");
// => [{myDivProp: "hello"}]
```

If the prop has multiple matches, all of them will be extracted

```html
<div class="divs">hello</div>
<div class="divs">hello again</div>
<div class="divs">hello again again</div>
```

```js
ayakashi.select("helloDiv").where({class: {eq: "divs"}});
const result = await ayakashi.extract("helloDiv");
// => [{helloDiv: "hello"}, {helloDiv: "hello again"}, {helloDiv: "hello again again"}]
```

## Attribute extraction

HTML attributes can be used as extractor names

```html
<a href="http://example.com" id="example">Click it!</a>
```

```js
ayakashi.selectOne("myLink").where({id: {eq: "example"}});
const result = await ayakashi.extract("myLink", "href");
// => [{myLink: "http://example.com"}]
```

## Regex extraction

Instead of an extractor, a regex can be used.  
It will return only the substring matched by the regex

```html
<div id="content">Here is my content: hello there</div>
```

```js
ayakashi.selectOne("myContentDiv").where({id: {eq: "content"}});
const result = await ayakashi.extract("myContentDiv", /hello there/);
// => [{myContentDiv: "hello there"}]
```

If the regex didn't match anything, an empty string will be returned

```html
<div class="divs">hello</div>
<div class="divs">hello again</div>
<div class="divs">hello again again</div>
```

```js
ayakashi.select("helloDiv").where({class: {eq: "divs"}});
const result = await ayakashi.extract("helloDiv", /hello again again/);
// => [{helloDiv: ""}, {helloDiv: ""}, {helloDiv: "hello again again"}]
```

## Extracting multiple values

Multiple values can be extracted from a prop match at once

```html
<a href="http://example.com" id="example">Click it!</a>
```

```js
ayakashi.selectOne("myLink").where({id: {eq: "example"}});
const result = await ayakashi.extract("myLink", {
    link: "href",
    text: "text"
});
// => [{link: "http://example.com", text: "Click it!"}]
```

The object notation can also be used if we want a key different than the prop name
(useful for anonymous props)

```html
<a href="http://example.com" id="example">Click it!</a>
```

```js
ayakashi.selectOne("myLink").where({id: {eq: "example"}});
const result = await ayakashi.extract("myLink", "href");
// => [{myLink: "http://example.com"}]
const result2 = await ayakashi.extract("myLink", {
    link: "href"
});
// => [{link: "http://example.com"}]
```

## Using defaults

Each extractor specifies a default value to be used if a valid value
is not found. This can be overwritten with our own defaults

```html
<div class="divs">hello</div>
<div class="divs"></div>
```

```js
ayakashi.select("helloDiv").where({class: {eq: "divs"}});
const result = await ayakashi.extract("helloDiv", ["text", "nothing to say"]);
// => [{helloDiv: "hello again again"}, {helloDiv: "nothing to say"}]
```

Overwritten defaults can be used with regexes and in object form as well.  
**Note:** a default value is used as is and is not evaluated

```js
const result = await ayakashi.extract("aLink", ["href", "id"]);
```

If a `href` is not found it will return the string `id` and not the actual
element's id.

## Using extraction functions

A function can also be used as an extractor.  
It takes the matched html element as an argument.  
It will be run for each matched element of a prop if there are multiple.

```html
<a href="http://example.com" id="example">Click it!</a>
```

```js
ayakashi.selectOne("myLink").where({id: {eq: "example"}});
const result = await ayakashi.extract("myLink", function(el) {
    return el.href;
});
// => [{myLink: "http://example.com"}]
```

No checks will be made for the return value and no defaults will be applied.  
They should be manually implemented in the function.

## Other builtin extractors

A couple more extractors are included by default and probably more will be
in the future.

* `integer` Extracts integers only and returns an integer data type
* `number` (alias of `integer`)
* `float` Extracts floating point numbers only and returns a float data type

## Creating your own extractors

Advanced
{: .label .label-red }

You can also create your own extractors, either from scratch or by extending extractors that are already available
(including the builtin ones). Learn how [here](/docs/advanced/creating-your-own-extractors.html).
