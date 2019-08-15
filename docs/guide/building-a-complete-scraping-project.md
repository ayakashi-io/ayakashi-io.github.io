---
layout: default
title: Building a complete scraping project
parent: Guide
nav_order: 3
---

<!-- markdownlint-disable MD022 -->
# Building a complete scraping project
{: .no_toc }
<!-- markdownlint-enable MD022 -->

In this section we will build a complete Ayakashi scraping project by re-using our [simple github scraper](/docs/guide/running-a-simple-scraper.html)
while extending its functionality and exploring how Ayakashi projects are structured.  
If you haven't read it already, take a minute to check the [simple github scraper](/docs/guide/running-a-simple-scraper.html)
we built in the first section and then the short Ayakashi [tour](/docs/guide/tour.html).

* Generating the project
* What we are going to build
* Our config
* The timespans script
* Getting the trending repos
  * Our scraper
  * I/O
  * Defining prop in prop files
  * Using params
  * Yielding data
* The repo info scraper
* Saving our data
* Let’s run it
* Parting words
{:toc}

## Generating the project

We can generate a new project by running:

```bash
ayakashi new githubComplete
```

The command will do the following:

* create a new folder `githubComplete` in your current directory
* generate an example `ayakashi.config.js`
* generate a `package.json` file with the local dependencies needed
* generate some example files to get you started
* run `npm install` to install the local dependencies

## What we are going to build

We will fetch the currently [trending repos of github](https://github.com/trending) for the day, week and month.  
Then pass the repo link to our info extracting scraper (from the [first section](/docs/guide/running-a-simple-scraper.html))
to get some data like the about message, star count and the git clone url.  
We will finally save our extracted data to JSON, CSV and an sqlite database.

## Our config

Replace the generated example `ayakashi.config.js` with:

```js
/**
* @type {import("@ayakashi/types").Config}
*/
module.exports = {
    config: {},
    waterfall: [{
        type: "script",
        module: "getTimespans"
    }, {
        type: "scraper",
        module: "getTrendingRepos",
        params: {
            repoLimit: 5
        },
        config: {
            retries: 5
        }
    }, {
        type: "scraper",
        module: "getRepoInfo",
        config: {
            retries: 5
        },
        parallel: [{
            type: "script",
            module: "saveToCSV"
        }, {
            type: "script",
            module: "saveToJSON"
        }, {
            type: "script",
            module: "saveToSQL"
        }]
    }]
};
```

Our [pipeline config](/docs/guide/tour.html#pipelines) describes exactly what we want to build.  
In a serial manner (waterfall) it will first run our script `getTimespans` which will pass the
timespans we want (daily, weekly, monthly) to our `getTrendingRepos` scraper which in turn will pass
each trending repository link to our `getRepoInfo` scraper to extract the data we want.  
Finally, our `getRepoInfo` will run three different saving scripts in parallel by passing the extracted data to each.  
We also specify some [retries](/docs/going_deeper/automatic_retries.html) in our scrapers to make sure they complete even if there is some network problem.

## The timespans script

Ayakashi includes generator commands for all of its components.  
Inside our new `githubComplete` folder, run:

```bash
ayakashi new --script --name=getTimespans
```

Replace the example content of `scripts/getTimespans.js` with:

```js
module.exports = async function(input, params) {
    return [{
        timespan: "daily"
    }, {
        timespan: "weekly"
    }, {
        timespan: "monthly"
    }];
};
```

Pretty simple. We could have probably hardcoded something like this directly in the scraper but there
is a twist here.  
Since we are returning an array, the next pipeline step will run for each member of the array.  
So, our `getTrendingRepos` scraper will run three times, each time with a different timespan.  
Make a note of this: **If a script returns an array, the next pipeline step will be run for each member of the array**.

## Getting the trending repos

### Our scraper

Again let's use a command to generate our scraper

```bash
ayakashi new --scraper --name=getTrendingRepos
```

Replace the example content in `scrapers/getTrendingRepos.js` with:

```js
/**
 * @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
 */
module.exports = async function(ayakashi, input, params) {
    console.log("getting trending repos:", input.timespan);

    //load the page with the timespan parameter we got from our script
    await ayakashi.goTo(`https://github.com/trending?since=${input.timespan}`);

    //wait for the trendingRepos prop to be loaded and visible
    await ayakashi.waitUntilVisible("trendingRepos");

    //extract the href from each entry of the list
    const trending = await ayakashi.extract("trendingRepos", "href");

    //limit our links based on the repoLimit parameter we have
    const limitedTrendingRepos = trending.slice(0, params.repoLimit);

    //yield each href to our pipeline
    await ayakashi.yieldEach(limitedTrendingRepos);
};
```

### I/O

Ok, there are a few things to notice here.  
First see how the timespan is being passed from our script and is available inside
the `input` argument which we use to load the page.  
**Outputs from the previous pipeline step are available as inputs in the next step**  

### Defining props in prop files

Then we wait for our `trendingRepos` to be loaded and visible. But where is the actual prop definition?  
If you remember in the [simple scraper](/docs/guide/running-a-simple-scraper.html) we built before, all
props were defined inside the scraper itself.  
We will do it differently here.  
Prop definitions can be placed inside their own files and then be available for every scraper to use.  
<br/>
Again, let's run a command to generate a prop:

```bash
ayakashi new --prop --name=trendingRepos
```

Open our new `props/trendingRepos.js` file and replace the example content with:

```js
/**
 * @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
 */
module.exports = function(ayakashi) {
    ayakashi
        .select("trendingRepos")
        .where({
            and: [{
                tagName: {
                    eq: "a"
                }
            }, {
                "style-font-size": {
                    eq: "20px"
                }
            }]
        });
};
```

The prop files will be loaded before our scraper has begun executing.  
For that reason there is no page access inside a prop file.  
**Prop files can only contain prop definitions.**  

### Using params

Next, we [extract](/docs/guide/data-extraction.html) the `href` of all of the matches of
the `trendingRepos` prop and store them in a `trending` variable.  
If you remember our config file, we used a `params` block in our scraper's definition:

```js
params: {
    repoLimit: 5
}
```

Anything placed in `params` of a scraper or script in the config file will then be available
for the scraper/script to use inside the `params` object.  
In our case we passed a `repoLimit` to limit the amount of trending repos to fetch.  
So we use our parameter and slice the original extracted list of links into a new `limitedTrendingRepos`
variable which will contain only the first five links.

### Yielding data

On the [simple scraper](/docs/guide/running-a-simple-scraper.html) we built before we just used a `return` to
return our data at the very end of the function, which is fine when we want to return a single piece
of data and then exit.  
By using [yield](docs/going_deeper/yielding-data.html) (`yieldEach()` in our example) we can generate results multiple
times and let the rest of our pipeline keep running in parallel with the data we keep on feeding.  
<br/>
**`yield` can be placed anywhere inside our scrapers and any number of times as well.**  
<br/>
A good example to better understand the usefulness of `yield`
would be to imagine scraping a page that uses infinite scrolling to load more and more data as we scroll.  
Instead of keeping all the data in memory stored in a variable and return it all at once at the end,
we can keep using `yield` after every time we scroll and get new data.  
This should greatly improve the
parallelism and performance of our scraper while being more readable and easier to understand.

## The repo info scraper

We will just re-use the [simple scraper](/docs/guide/running-a-simple-scraper.html) we built before but this time
move its props to their own files and make it accept the repo link as input.  
Let's generate the file and replace the example content:

```bash
ayakashi new --scraper --name=getRepoInfo
```

```js
/**
 * @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
 */
module.exports = async function(ayakashi, input, params) {
    console.log("getting info for:", input);

    //go to the repo page
    await ayakashi.goTo(input);

    //wait until the about section is loaded and visible
    await ayakashi.waitUntilVisible("about");

    //extract the about message
    const about = await ayakashi.extractFirst("about", "text");

    //extract stars count
    const stars = await ayakashi.extractFirst("stars", "number");

    //click the clone button
    await ayakashi.click("cloneDialogTrigger");

    //extract the clone url
    const cloneUrl = await ayakashi.extractFirst("cloneUrl", "value");

    //return our results
    return {about, stars, cloneUrl};
};
```

Our `getRepoInfo` will run for each repo link we yield in our `getTrendingRepos` scraper.  

And here are our props (check the new prop command from above on how to generate the files).  

`props/infoProps.js`

```js
/**
 * @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
 */
module.exports = function(ayakashi) {
    ayakashi
        .selectOne("about")
        .where({itemprop: {eq: "about"}});
    ayakashi
        .selectOne("stars")
        .where({href: {like: "/stargazers"}});
    ayakashi
        .selectOne("cloneUrl")
        .where({"aria-label": {like: "Clone this repository at"}});
};
```

`props/cloneDialogTrigger.js`

```js
/**
 * @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
 */
module.exports = function(ayakashi) {
    ayakashi
        .selectOne("cloneDialogTrigger")
        .where({
            and: [{
                class: {
                    eq: "btn"
                }
            }, {
                "style-background-color": {
                    eq: "rgb(40, 167, 69)"
                }
            }, {
                textContent: {
                    like: "Clone"
                }
            }]
        });
};
```

You can place each prop in each own file or group them together.  
Here i placed all the info props together and the button trigger alone.

## Saving our data

Finally, let's save our data.  
We nested a parallel step inside our `getRepoInfo` scraper to save our data in
CSV, JSON and an sqlite database in parallel.

```js
parallel: [{
    type: "script",
    module: "saveToCSV"
}, {
    type: "script",
    module: "saveToJSON"
}, {
    type: "script",
    module: "saveToSQL"
}]
```

## Let's run it

Inside our project's root directory run:

```bash
ayakashi run
```

It will load our config and start executing our pipeline while printing some progress info.  
After it's done, it will create a `data` directory in our project folder containing a subfolder with the
start datetime as a name and inside it three different files. A `data.json`, `data.csv` and `database.sqlite` with our data.

## Parting words

In this section we built a complete project by using a more complex pipeline that utilizes
multiple scrapers and scripts, running some of them in order and others in parallel.  
We also learned about prop files, `yield` and passing input and params.  
<br/>
Something to keep in mind is that you don't have to use every available tool and function, just knowing that it's there
and having choice is enough.  
Sometimes a simple, [single file scraper](/docs/guide/running-a-simple-scraper.html) will be a better choice than a full
blown project or inlining the props instead of placing them in their own files
or just using a simple `return` instead of [yield](/docs/going_deeper/yielding-data.html).  
Use whatever best fits your usecase and aids in the readability and maintainability of your scrapers.
