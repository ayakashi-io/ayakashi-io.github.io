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

Ayakashi comes bundled with a set of scripts to help saving extracted data without writing any extra code.  

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
For that reason they will only work with data in the format returned by using [extract](/docs/guide/data-extraction.html)
with props.  
Both a single extracted prop and multiple extracted props wrapped in an object work:

```js
return ayakashi.extract("myProp", "text");
```

```js
const data1 = ayakashi.extract("myProp1", "text");
const data2 = ayakashi.extract("myProp2", "text");
const data3 = ayakashi.extract("myProp3", "text");

return {data1, data2, data3}
```

When wrapping multiple extracted props in an object and these props contain multiple matches, the data will be grouped correctly
and normalized into proper rows.

## printToConsole

Not exactly a saving script, the `printToConsole` script will just print the data passed to it in a table format.  
Can really help while developing.  
Use it like any other script by including in your [pipeline](/docs/guide/tour.html#pipelines) after the step that
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
