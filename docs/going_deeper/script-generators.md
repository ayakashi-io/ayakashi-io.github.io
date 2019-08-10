---
layout: default
title: Script generators
parent: Going Deeper
nav_order: 10
---

# Script generators

Ayakashi [scripts](/docs/guide/tour.html#scripts) can return multiple results by returning a [Generator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators) (or `AsyncGenerator`).  
The next step of the [pipeline](/docs/guide/tour.html#pipelines) will be triggered for each value yielded by the generator.  
Let's see an example.  
<br/>

Imaging we need to get some values from our database (eg. some links) and for each value run a scraper.  
We could write the following script:

```js
module.exports = async function() {
    let skip = 0;

    return async function*() {
        while (true) {
            //using a database ORM
            const myLink = await LinkModel.findOne({
                where: {
                    //some condition
                },
                skip: skip
            });

            //stop the generator
            if (!myLink) {
                break;
            }

            //update skip
            skip += 1;

            //yield our result
            yield {link: myLink.someColumn};
        }
    };
};
```

Ayakashi will keep executing the generator until no more results are available and run the next
step of the pipeline with an input of `{link: "someValue"}` for each of the yielded values.

## Some notes

Instead of using a generator we could have just returned all of our values at once in an array.  
Ayakashi would still pass each value of the array individually to the next step of the pipeline.  
For small datasets this is probably ok but imagine a database with 1M rows and we need to do something
for each one. Dumping everything to Ayakashi in one go will put unnecessary stress on our db.  
Fetching one by one is no better though so a good solution in this case would be to mix the two.  
Use a Generator but yield a batch of X values each time in an array.
