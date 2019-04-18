---
layout: default
title: Introduction
nav_order: 1
has_children: false
permalink: /docs/introduction
---

# Ayakashi.io

## The next generation web scraping framework

The web has changed. Gone are the days that raw html parsing scripts were the proper tool for the job.  
Javascript and single page applications are now the norm.  
Demand for data scraping and automation is higher than ever,
from business needs to data science and machine learning.  
Our tools need to evolve.

### Ayakashi helps you build scraping and automation systems that are

* easy to build
* simple or sophisticated
* highly performant
* maintainable and built for change

### Powerful querying and data models

Ayakashi's way of finding things in the page and using them is
done with [props](/docs/guide/tour.html#props) and [domQL](/docs/guide/querying-with-domql.html).  
Directly inspired by the relational database world (and SQL), domQL makes
DOM access easy and readable no matter how obscure the page's structure is.  
Props are the way to package domQL expressions as re-usable structures which
can then be passed around to [actions](/docs/guide/tour.html#actions) or to be used as models for [data
extraction](/docs/guide/data-extraction.html).  
Tl;dr instead of this:  

<!-- markdownlint-disable MD013 -->

```js
document.querySelector('#js-repo-pjax-container > div.container.new-discussion-timeline.experiment-repo-nav > div.repository-content > div.file-navigation.in-mid-page.d-flex.flex-items-start > details.get-repo-select-menu.js-get-repo-select-menu.position-relative.details-overlay.details-reset > summary');
```

you can now write this:  

![domql](/assets/img/domql.png)
<!-- markdownlint-enable MD013 -->

### High level builtin actions

Ready made actions so you can focus on what matters.  
Easily handle infinite scrolling, single page navigation, events
and [more](/docs/reference/builtin-actions.html).  
Plus, you can always [build your own actions](/docs/advanced/creating-your-own-actions.html), either from scratch or by composing
other actions.

### Preload code on pages

Need to include a bunch of code, a library you [made](/docs/advanced/creating-your-own-preloaders.html) or a
[3rd party module](/docs/going_deeper/loading-libraries-as-preloaders.html) and make it available on a page?  
[Preloaders](/docs/guide/tour.html#preloaders) have you covered.

### Control how you save your data

Automatically save your extracted data
to [all major SQL engines, JSON and CSV.](/docs/guide/builtin-saving-scripts.html)  
Need something more exotic or the ability to control exactly how the data is persisted?  
Package and plug your custom logic as a script.

### Manage the flow with pipelines

Scraping the data is only one part of the deal.  
How about something like this:  

![pipelines](/assets/img/diagram.png)

Need it to also be clean, readable and performant? If so, [pipelines](/docs/guide/tour.html#pipelines) can help.

### Utilize all your cores

Ayakashi can utilize available cores as needed. Especially useful for projects that need
to run multiple operations in parallel.

### Extend it as you like

All APIs used to build the builtin functionality are properly exposed.  
All core entities are composable and extensible.

### Use the language of the web

Many argue about javascript and its quirkiness as a language.  
But the truth is, if you want to scrape the web, you should speak its language.

### Great editor support

Ayakashi comes bundled with a fully documented public API that you can explore
directly in your editor.  
Autocomplete any method, check signatures and examples or follow links to more documentation.  
<br/>
![editor support](/assets/img/editor.png)
<br/><br/>
Sounds cool?  
Just head over to the [getting started guide](/docs/getting_started)!