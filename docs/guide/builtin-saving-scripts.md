---
layout: default
title: Builtin saving scripts
parent: Guide
nav_order: 7
---

<!-- markdownlint-disable MD022 -->
# Builtin saving scripts
{: .no_toc }
<!-- markdownlint-enable MD022 -->

Ayakashi comes bundled with a set of scripts to help persisting extracted data without writing any extra code.  

<!-- markdownlint-disable MD022 -->
## Table of contents
{: .no_toc .text-delta }
<!-- markdownlint-enable MD022 -->

* Limitations
* printToConsole
* saveToJSON
* saveToCSV
* saveToSQL
  * Dialect
  * Connection options
  * Table name
  * dataTypes
{:toc}

## Limitations

The builtin saving scripts are designed to work out of the box without the need of any extra code.  
In order for this to work they need to be wrapped in an object with a key of your choice so they can
be mapped into proper sql rows, csv lines and json objects.  

Returning a single prop result can be done like this:

```js
return {
    someKey: await ayakashi.extractFirst("myProp", "text")
};
```

And for multiple prop results:

```js
const data1 = await ayakashi.extractFirst("myProp1", "text");
const data2 = await ayakashi.extractFirst("myProp2", "text");
const data3 = await ayakashi.extractFirst("myProp3", "text");

return {
    key1: data1,
    key2: data2,
    key3: data3
};
```

If you need to return groups of multiple data sets please take a look
in the [Grouping extracted data](/docs/guide/data-extraction.html#grouping-extracted-data) section.

## printToConsole

Not exactly a saving script, the `printToConsole` script will just print the data passed to it in a table format.  
Can really help while developing.  
Use it like any other script by including it in your [pipeline](/docs/guide/tour.html#pipelines) after the step that
outputs the data you want to print:

```js
{
    waterfall: [{
        type: "scraper",
        module: "myScraper"
    }, {
        type: "script",
        module: "printToConsole"
    }]
}
```

## saveToJSON

The `saveToJSON` will persist the data in a json file in a performant way

```js
{
    waterfall: [{
        type: "scraper",
        module: "myScraper"
    }, {
        type: "script",
        module: "saveToJSON",
        params: {
            file: "myData.json"
        }
    }]
}
```

You may configure the filename of the generated json file by passing a `file` param.  
By default it will output a `data.json` file.

## saveToCSV

The `saveToCSV` will persist the data in a [csv](https://en.wikipedia.org/wiki/Comma-separated_values)
file in a performant way

```js
{
    waterfall: [{
        type: "scraper",
        module: "myScraper"
    }, {
        type: "script",
        module: "saveToCSV",
        params: {
            file: "myData.csv"
        }
    }]
}
```

You may configure the filename of the generated csv file by passing a `file` param.  
By default it will output a `data.csv` file.

## saveToSQL

The `saveToSQL` script allows saving data to all of the major SQL engines in a table format

```js
{
    waterfall: [{
        type: "scraper",
        module: "myScraper"
    }, {
        type: "script",
        module: "saveToSQL",
        params: {}
    }]
}
```

There are a lot of parameters you can configure:

```ts
params: {
    dialect?: "mysql" | "mariadb" | "postgres" | "mssql" | "sqlite",
    host?: string,
    port?: number,
    database?: string,
    username?: string,
    password?: string,
    connectionURI?: string,
    table?: string
}
```

### Dialect

You may use any of the following dialects:

* [MySQL](https://www.mysql.com/) - `mysql`
* [PostgreSQL](https://www.postgresql.org/) - `postgres`
* [MariaDB](https://mariadb.org/) - `mariadb`
* [MSSQL - Microsoft SQL Server](https://en.wikipedia.org/wiki/Microsoft_SQL_Server) - `mssql`
* [SQLite](https://www.sqlite.org/index.html) - `sqlite`

**If no dialect is defined, `sqlite` is used by default**.

### Connection options

In all cases except of `sqlite` you probably need to supply some kind of connection options.  
You may use the `host`, `port`, `database`, `username` and `password` options or construct a
`connectionURI` and use that instead.  
If using `sqlite` only the `database` option has effect and it will change the database filename.
By default a `database.sqlite` filename is used.

### Table name

You can change the table name in any dialect by specifying the `table` option.  
If no `table` option is provided an internal uuid will be used.

### dataTypes

Supported dataTypes are the following:

* booleans
* integers
* floats
* objects
* strings

Any other dataType will be saved as a **string**.  
<br/>
Objects will be saved in a JSON column if the dialect is set to `postgres` or `sqlite`.  
For all the others a TEXT field will be used.
