---
layout: default
title: Building a complete scraping project
parent: Guide
nav_order: 3
---

# Building a complete scraping project

In this section we will build a complete Ayakashi scraping project by re-using our [simple github scrapper](/docs/guide/running-a-simple-scrapper.html)
while extending its functionality and exploring how Ayakashi projects are structured.  
If you haven't read it already, take a minute to check the [simple github scrapper](/docs/guide/running-a-simple-scrapper.html)
we built in the first section and then the short Ayakashi [tour](/docs/guide/tour.html).

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
Then pass the repo link to our info extracting scrapper (from the [first section](/docs/guide/running-a-simple-scrapper.html))
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
        type: "scrapper",
        module: "getTrendingRepos",
        params: {
            repoLimit: 5
        }
    }, {
        type: "scrapper",
        module: "getRepoInfo",
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
timespans we want (daily, weekly, monthly) to our `getTrendingRepos` scrapper which in turn will pass
each trending repository link to our `getRepoInfo` scrapper to extract the data we want.  
Finally, our `getRepoInfo` will run three different saving scripts in parallel by passing the extracted data to each.

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

Pretty simple. We could have probably hardcoded something like this directly in the scrapper but there
is a twist here.  
Since we are returning an array, the next pipeline step will run for each member of the array.  
So, our `getTrendingRepos` scrapper will run three times, each time with a different timespan.  
Make a note of this: **If a script returns an array, the next pipeline step will be run for each member of the array**.

## Getting the trending repos

### Our scrapper

Again let's use a command to generate our scrapper

```bash
ayakashi new --scrapper --name=getTrendingRepos
```

Replace the example content in `scrappers/getTrendingRepos.js` with:

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
    const trending = await ayakashi.extract("trendingRepos", {
        repoLink: "href"
    });

    //limit our links based on the repoLimit parameter we have
    const limitedTrendingRepos = trending.slice(0, params.repoLimit);

    //yield each repoLink to our pipeline
    for (const repoLink of limitedTrendingRepos) {
        await ayakashi.yield(repoLink);
    }
};
```

### The input object

Ok, there are a few things to notice here.  
First see how the timespan is being passed from our script and is available inside
the `input` object which we use to load the page.  
**Data passed from previous pipeline step are available in an input object.**  

### Defining prop in prop files

Then we wait for our `trendingRepos` to be loaded and visible. But where is the actual prop definition?  
If you remember in the [simple scrapper](/docs/guide/running-a-simple-scrapper.html) we built before, all
props were defined inside the scrapper itself.  
We will do it differently here.  
Prop definitions can be placed inside their own files and then be available for every scrapper to use.  
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
                    eq: "A"
                }
            }, {
                "style-font-size": {
                    eq: "20px"
                }
            }]
        });
};
```

The prop files will be loaded before our scrapper has begun executing.  
For that reason there is no page access inside a prop file.  
**Prop files can only contain prop definitions.**  

### Using params

Next, we [extract](/docs/guide/data-extraction.html) the `href` of all of the matches of
the `trendingRepos` prop and store them in a `trending` variable.  
If you remember our config file, we used a `params` block in our scrapper's definition:

```js
params: {
    repoLimit: 5
}
```

Anything placed in `params` of a scrapper or script in the config file will then be available
for the scrapper/script to use inside the `params` object.  
In our case we passed a `repoLimit` to limit the amount of trending repos to fetch to five.  
So, we use our parameter and slice the original extracted list of links into a new `limitedTrendingRepos`
variable which will contain only the first five links.

### Yielding data

On the [simple scrapper](/docs/guide/running-a-simple-scrapper.html) we built before we just used a `return` to
return our data at the very end of the function, which is fine when we want to return a single piece
of data and then exit.  
Using `yield` we can generate results multiple times and let the rest of our pipeline keep running in parallel with
the data we keep on feeding.  
<br/>
**`yield` can be placed anywhere inside our scrappers and any number of times as well.**  
<br/>
A good example to better understand the usefulness of `yield`
would be to imagine scraping a page that uses infinite scrolling to load more and more data as we scroll.  
Instead of keeping all the data in memory stored in a variable and return it all at once at the end,
we can keep using `yield` after every time we scroll and get new data.  This should greatly improve the
parallelism and performance of our scrapper while being more readable and easier to understand.

## The repo info scrapper

We will just re-use the [simple scrapper](/docs/guide/running-a-simple-scrapper.html) we built before but this time
move its props to their own files and make it accept the repo link as input.  
Let's generate the file and replace the example content:

```bash
ayakashi new --scrapper --name=getRepoInfo
```

```js
/**
 * @param {import("@ayakashi/types").IAyakashiInstance} ayakashi
 */
module.exports = async function(ayakashi, input, params) {
    console.log("getting info for:", input.repoLink);

    //go to the repo page
    await ayakashi.goTo(input.repoLink);

    //wait until the about section is loaded and visible
    await ayakashi.waitUntilVisible("about");

    //extract the about message
    const about = await ayakashi.extract("about", "text");

    //extract stars count
    const stars = await ayakashi.extract("stars", "number");

    //click the clone button
    await ayakashi.click("cloneDialogTrigger");

    //extract the clone url
    const cloneUrl = await ayakashi.extract("cloneUrl", "value");

    //return our results
    return {about, stars, cloneUrl};
};
```

Our `getRepoInfo` will run for each repo link we yield in our `getTrendingRepos` scrapper.  

And here are our props (check the new prop command from above to generate the files).  

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

### Saving our data

Finally, let's save our data.  
We nested a parallel step inside our `getRepoInfo` scrapper to save our data in
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

### Let's run it

Inside our project's root directory run:

```bash
ayakashi run
```

It will load our config and start executing our pipeline while printing some progress info.  
After it's done, it will create a `data` directory in our project folder containing a subfolder with the
start datetime as a name and inside it three different files. A `data.json`, `data.csv` and `database.sqlite` with our data.

### Parting words

In this section we built a complete project by using a more complex pipeline that utilizes
multiple scrappers and scripts, running some of them in order and others in parallel.  
We also learned about prop files, `yield` and passing input and params.  
<br/>
Something to keep in mind is that you don't have to use every available tool and function, just knowing that it's there
and having choice is enough.  
Sometimes a simple, [single file scrapper](/docs/guide/running-a-simple-scrapper.html) will be a better choice than a full
blown project or inlining the props instead of placing them in their own files
or just using a simple `return` instead of `yield`.  
Use whatever best fits your usecase and aids in the readability and maintainability of your scrappers.
