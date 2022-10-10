---
layout: default
title: CLI commands and options
parent: Reference
nav_order: 2
---

<!-- markdownlint-disable MD022 -->
# CLI commands and options
{: .no_toc }
<!-- markdownlint-enable MD022 -->

Complete reference of all the CLI commands and their options.  
Can be also viewed in your console by running `ayakashi --help` or by appending `--help` after any command.

* run
  * run arguments
  * Examples
* new
  * Examples
* update-chrome
  * Examples
* update-ua
  * Examples
* update-stealth
  * Examples
* info
  * Examples
{:toc}

## run

Runs a project.

| Option | Description | Default |
| --- | --- |
| `dir` | The root directory of a project or a scraper file when `--simple` mode is used | `.` (current directory)

### Run arguments

| Option | Description | Default |
| --- | --- |
| `--configFile` or `-c` | Use an alternative configFile | `ayakashi.config.js`
| `--jsonConfig` or `-jc` | Use a json string as config |
| `--sessionKey` | Use a specific run session | `default`
| `--simple` | Run a single [scraper](/docs/guide/running-a-simple-scraper.html) | `false`
| `--simpleRenderless` | Run a single [renderlessScraper](/docs/guide/renderless-scrapers.html)| `false`
| `--simpleApi` | Run a single [apiScraper](/docs/guide/api-scrapers.html) | `false`
| `--out` | Select the saving format when `--simple` mode is used. Available formats: `sqlite`, `csv`, `json`, `stdout` | `stdout`
| `--resume` | Resume execution of a previous unfinished run | `false`
| `--restartDisabledSteps` | Will restart all steps that terminated due to an error. Only works when `--resume` is used | `false`
| `--clean` | Clear the previous run if it exists and start from the beginning | `false`
| `--skipTsBuild` | Skip automatic typescript compilation | `false`

### Examples

```bash
ayakashi run
```

```bash
ayakashi run ./myProject
```

```bash
ayakashi run --configFile=otherConfig.js ./myProject
```

```bash
ayakashi run --simple ./myProject/myScraper.js --out=json
```

```bash
ayakashi run --configFile alternative_config.js
```

```bash
ayakashi run --jsonConfig '{"config":{},"waterfall":[{"type":"apiScraper","module":"myScraper"}]}'
```

```bash
ayakashi run ./myProject --resume --sesionKey my_session
```

## new

Generates a new `project|scraper|renderlessScraper|apiScraper|script|prop|action|extractor|preloader`.

| Option | Description | Default |
| --- | --- |
| `dir` | Where to place the generated files | `.` (current directory)
| `--ts` | Generate a typescript project | `false` (show prompt)
| `--js` | Generate a javascript project | `false` (show prompt)

### Examples

```bash
ayakashi new
```

```bash
ayakashi new ./existingFolder
```

The command will ask you if you want to generate a Javascript or Typescript project. Pass `--ts` or `--js` to disable the prompt.

```bash
ayakashi new --extractor --name=myExtractor
```

```bash
ayakashi new --renderlessScraper --name=myScraper
```

If `--name` is omitted, an interactive prompt will be shown to enter the name:

```bash
ayakashi new --preloader
# Enter a name for the new preloader:
```

## update-chrome

Downloads the recommended, latest or specified chromium revision

### Examples

```bash
#downloads the recommended revision
ayakashi update-chrome

#downloads the latest available revision
ayakashi update-chrome --latest

#downloads a specific revision
ayakashi update-chrome -r 1045629
```

## update-ua

Updates the builtin database of user agent strings

### Examples

```bash
ayakashi update-ua
```

## update-stealth

Updates the headless chromium stealth patches

### Examples

```bash
ayakashi update-stealth
```

## info

Show the installed Ayakashi version and chromium revision.

### Examples

```bash
ayakashi info
```
