---
layout: default
title: The Ayakashi configuration file
parent: Reference
nav_order: 1
---
<!-- markdownlint-disable MD022 -->
# The Ayakashi configuration file
{: .no_toc }
<!-- markdownlint-enable MD022 -->

The complete configuration reference of the `ayakashi.config.js` file.

<!-- markdownlint-disable MD022 -->
## Table of contents
{: .no_toc .text-delta }
<!-- markdownlint-enable MD022 -->

* Global configuration options
* Step configuration options
  * Extra step configuration
  * Step loading options
    * Extra preloader options
{:toc}

## Global configuration options

Options for the top-level `config` object.

| Option | Type | Description |
| --- | --- |
| `headless` | boolean | Setting it to `false` will disable headless mode. |
| `userAgent` | string | Configures the userAgent for all scrapers. By default a random userAgent is used. Valid values are `desktop`, `mobile`, `random`. |
| `proxyUrl` | string | Sets a proxy url for all scrapers. |
| `openDevTools` | boolean | Automatically open devTools for every tab. `headless` must be `false` for it to have any effect. |
| `persistentSession` | boolean | Persists the session data of all pages instead of using a temporary session each time. Learn more [here](/docs/going_deeper/persisting-sessions.html) |
| `windowWidth` | number | Sets the width of the browser window. Default is `1920`. |
| `windowHeight` | number | Sets the height of the browser window. Default is `1080`. |
| `chromePath` | string | Use a custom chrome/chromium executable instead of the auto-downloaded one. Learn more [here](/docs/going_deeper/using-a-different-chrome.html) |
| `ignoreCertificateErrors` | boolean | Ignore all certificate (ssl) errors. |
| `bridgePort` | number | Sets the port of the internal bridge server, Default is `9731`. |
| `protocolPort` | number | Sets the port of the internal devTools protocol server, Default is `9730`. |

## Step configuration options

The configuration options used for steps inside `parallel` or `waterfall`.

| Option | Type | Description |
| --- | --- |
| `type` | string | The type of the step. Valid values are `scraper`, `renderlessScraper`, `apiScraper` or `script`. |
| `module` | string | The name of the module. |
| `params` | object | A custom parameters object to pass to the module. |
| `config` | object | Extra configuration for the step. See below |
| `load` | object | Specify external modules that should be loaded by the scraper. See below |

### Extra step configuration

Options for a step's `config` object.

| Option | Type | Description |
| --- | --- |
| `pipeConsole` | boolean | Set it to `false` to disable the page console logs from getting printed. |
| `pipeExceptions` | boolean | Set it to `false` to disable any page uncaught exceptions from getting printed. |
| `localAutoLoad` | boolean | Set it to `false` to disable automatic loading of local `actions`, `extractors`, `preloaders` and `props` |
| `emulatorOptions` | object | Emulation options for the scraper to use. You can emulate the available `width` and `height` of the "device". Set `mobile` to `true` to emulate a mobile device. |

### Step loading options

Options for a step's `load` object.

| Option | Type | Description |
| --- | --- |
| `extractors` | array of strings | An array of external extractor modules. |
| `actions` | array of strings | An array of external action modules. |
| `preloaders` | array of strings or objects | An array of external preloader modules. |

#### Extra preloader options

`load.preloaders` can also accept object elements. Here are the options

| Option | Type | Description |
| --- | --- |
| `module` | string | The preloader's module name. |
| `as` | string | Set a custom name for the preloader. |
| `waitForDom` | boolean | Set it to `true` to wait for the DOM to be ready before loading the preloader. |
