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
* HTML property extraction
* HTML attribute extraction
* HTML data-attribute extraction
* Regex extraction
* Using defaults
* Using extraction functions
* Other builtin extractors
* extractFirst()
* extractLast()
* Grouping extracted data
* Creating your own extractors
{:toc}

## Text extraction

The simplest possible extraction

```js
const result = await ayakashi.extract("myProp", "text");
```

If no extractor is specified, the `text` extractor is used.  
This is equivalent to the above:

```js
const result = await ayakashi.extract("myProp");
```

So for the following html:

```html
<div id="myDiv">hello</div>
```

The result will be this:

```js
ayakashi.select("myDivProp").where({id: {eq: "myDiv"}});
const result = await ayakashi.extract("myDivProp");
// => ["hello"]
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
// => ["hello", "hello again", "hello again again"]
```

If the prop has no matches, an empty array `[]` will be returned.

## HTML property extraction

HTML properties can be used as extractor names

```html
<a href="http://example.com" id="example">Click it!</a>
```

```js
ayakashi.selectOne("myLink").where({id: {eq: "example"}});
const result = await ayakashi.extract("myLink", "href");
// => ["http://example.com"]
```

## HTML attribute extraction

HTML attributes can be used as extractor names

```html
<a href="http://example.com" id="example" title="this is a link">Click it!</a>
```

```js
ayakashi.selectOne("myLink").where({id: {eq: "example"}});
const result = await ayakashi.extract("myLink", "title");
// => ["this is a link"]
```

## HTML data-attribute extraction

HTML data-attributes can be used as extractor names

```html
<a href="http://example.com" id="example" data-my-key="some value">Click it!</a>
```

```js
ayakashi.selectOne("myLink").where({id: {eq: "example"}});
const result = await ayakashi.extract("myLink", "data-my-key");
// => ["some value"]
```

A [dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset) camelCased name can be used as well

```js
ayakashi.selectOne("myLink").where({id: {eq: "example"}});
const result = await ayakashi.extract("myLink", "myKey");
// => ["some value"]
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
// => ["hello there"]
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
// => ["", "", "hello again again"]
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
// => ["hello", "nothing to say"]
```

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
// => ["http://example.com"]
```

No checks will be made for the return value and no defaults will be applied.  
They should be manually implemented in the function.

## Other builtin extractors

A couple more extractors are included by default and probably more will be
in the future.

* `integer` Extracts integers only and returns an integer data type
* `number` (alias of `integer`)
* `float` Extracts floating point numbers only and returns a float data type

## extractFirst()

`extractFirst()` can be used instead of `extract()` and it will extract data only from the first match of a prop.

```html
<div class="divs">hello</div>
<div class="divs">hello again</div>
<div class="divs">hello again again</div>
```

```js
ayakashi.select("helloDiv").where({class: {eq: "divs"}});
const result = await ayakashi.extractFirst("helloDiv");
// => "hello"
```

If the prop has no matches, `extractFirst()` will return `null`.

## extractLast()

`extractLast()` can be used instead of `extract()` and it will extract data only from the last match of a prop.

```html
<div class="divs">hello</div>
<div class="divs">hello again</div>
<div class="divs">hello again again</div>
```

```js
ayakashi.select("helloDiv").where({class: {eq: "divs"}});
const result = await ayakashi.extractLast("helloDiv");
// => "hello again again"
```

If the prop has no matches, `extractLast()` will return `null`.

## Grouping extracted data

Many times when we extract multiple sets of related data from a page we probably want to group them together.  
Imagine the following html:

```html
<div class="container">
    <label>Link 1</label>
    <a href="http://example.com">click me</a>
</div>
<div class="container">
    <label>Link 2</label>
    <a href="http://example2.com">click me</a>
</div>
<div class="container">
    <label>Link 3</label>
    <a href="http://example3.com">click me</a>
</div>
```

Here, each link belongs with its label. Let' see how we can group them.

```js
//first let's define our props
ayakashi
    .select("parent")
    .where({
        class: {
            eq: "container"
        }
    })
    .trackMissingChildren();
ayakashi
    .select("links")
        .where({
            tagName: {
                eq: "a"
            }
        })
        .from("parent");
ayakashi
    .select("labels")
        .where({
            tagName: {
                eq: "label"
            }
        })
        .from("parent");

//let's extract them
const links = await ayakashi.extract("links", "href");
// => ["http://example.com", "http://example2.com", "http://example3.com"]
const labels = await ayakashi.extract("labels");
// => ["Link 1", "Link 2", "Link 3"]

//we can use ayakashi.join() to group them together
const groupedData = ayakashi.join({
    link: links
    label: labels
});

//this is the grouped result
console.log(groupedData);
/*
=> [{
    link: "http://example.com",
    label: "Link 1"
}, {
    link: "http://example2.com",
    label: "Link 2"
}, {
    link: "http://example3.com",
    label: "Link 3"
}]
*/
```

`ayakashi.join()` will join all arrays together into groups based on their index.  
If all of the array values are not of the same length an error will be thrown.  
If a non-array value is used it will be copied to every group.  
[trackMissingChildren()](http://localhost:4000/docs/guide/querying-with-domql.html#tracking-missing-children)
should be used in cases like these. It will ensure proper array lengths and correct ordering as long as a common
parent prop is used.

## Creating your own extractors

Advanced
{: .label .label-red }

You can also create your own extractors, either from scratch or by extending extractors that are already available
(including the builtin ones). Learn how [here](/docs/advanced/creating-your-own-extractors.html).
