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
* get-chrome
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
| `--simple` | Run a single scraper | `false`
| `--out` | Select the saving format when `--simple` mode is used. Available formats: `sqlite`, `csv`, `json`, `stdout` | `stdout`
| `--resume` | Resume execution of a previous unfinished run | `false`
| `--restartDisabledSteps` | Will restart all steps that terminated due to an error. Only works when --resume is used | `false`
| `--clean` | Clear the previous run if it exists and start from the beginning | `false`

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

## new

Generates a new `project|scraper|renderlessScraper|apiScraper|script|prop|action|extractor|preloader`.

| Option | Description | Default |
| --- | --- |
| `dir` | Where to place the generated files | `.` (current directory)

### Examples

```bash
ayakashi new
```

```bash
ayakashi new ./existingFolder
```

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

Updates/Downloads the latest chromium revision.

### Examples

```bash
ayakashi update-chrome
```

## get-chrome

Downloads the latest chromium revision if one is not already installed.

### Examples

```bash
ayakashi get-chrome
```
## info

Show the installed Ayakashi version and chromium revision.

### Examples

```bash
ayakashi info
```
