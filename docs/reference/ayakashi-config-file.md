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
| `proxyUrl` | string | Sets a proxy url for all scrapers. |
| `openDevTools` | boolean | Automatically open devTools for every tab. `headless` must be `false` for it to have any effect. |
| `persistentSession` | boolean | Persists the session data of all scrapers instead of using a temporary session each time. Learn more [here](/docs/going_deeper/persisting-sessions.html) |
| `windowWidth` | number | Sets the width of the browser window. Default is `1920`. |
| `windowHeight` | number | Sets the height of the browser window. Default is `1080`. |
| `chromePath` | string | Use a custom chrome/chromium executable instead of the auto-downloaded one. Learn more [here](/docs/going_deeper/using-a-different-chrome.html) |
| `ignoreCertificateErrors` | boolean | Ignore all certificate (ssl) errors. |
| `bridgePort` | number | Sets the port of the internal bridge server, default is `9731`. Use `0` for a random port. |
| `protocolPort` | number | Sets the port of the internal devTools protocol server, default is `9730`. Use `0` for a random port. |
| `workers` | number | Sets the number of workers to use. Defaults to `auto` which will spawn as many workers as needed by the current pipeline (not more than the total system thread count). |
| `workerConcurrency` | number | Sets the number of steps each worker can execute at the same time. Defaults to `1`. It should only be increased if there are more parallel steps than the worker count and the steps are mostly I/O bound. |

## Step configuration options

The configuration options used for steps inside `parallel` or `waterfall`.

| Option | Type | Description |
| --- | --- |
| `type` | string | The type of the step. Valid values are `scraper`, `renderlessScraper`, `apiScraper` or `script`. |
| `module` | string | The name of the module. |
| `params` | object | A custom parameters object to pass to the module. |
| `config` | object | Extra configuration for the step. See [below](/docs/reference/ayakashi-config-file.html#extra-step-configuration) |
| `load` | object | Specify external modules that should be loaded by the scraper. See [below](/docs/reference/ayakashi-config-file.html#step-loading-options) |

### Extra step configuration

Options for a step's `config` object.

| Option | Type | Description |
| --- | --- |
| `pipeConsole` | boolean | Set it to `false` to disable the page console logs from getting printed. |
| `pipeExceptions` | boolean | Set it to `false` to disable any page uncaught exceptions from getting printed. |
| `localAutoLoad` | boolean | Set it to `false` to disable automatic loading of local `actions`, `extractors`, `preloaders` and `props` |
| `emulatorOptions` | object | Specify emulation options to be used by this scraper. See [below](/docs/reference/ayakashi-config-file.html#emulatoroptions) |

### Step loading options

Options for a step's `load` object.

| Option | Type | Description |
| --- | --- |
| `extractors` | array of strings | An array of external extractor modules. |
| `actions` | array of strings | An array of external action modules. |
| `preloaders` | array of strings or [objects](/docs/reference/ayakashi-config-file.html#extra-preloader-options) | An array of external preloader modules. |

#### Extra preloader options

`load.preloaders` can also accept object elements. Here are the options

| Option | Type | Description |
| --- | --- |
| `module` | string | The preloader's module name. |
| `as` | string | Set a custom name for the preloader. |
| `waitForDom` | boolean | Set it to `true` to wait for the DOM to be ready before loading the preloader. |

### emulatorOptions

| Option | Type | Description |
| --- | --- |
| `width` | number | Sets the available width. |
| `height` | number | Sets the available height. |
| `mobile` | boolean | Set it to `true` to emulate a mobile device. |
| `userAgent` | `random` / `desktop` / `mobile` / `chrome-desktop` / `chrome-mobile` | Configures the userAgent for this scraper. By default a random `chrome-desktop` userAgent is used. |
| `acceptLanguage` | string | Configures the language for this scraper. It will be applied as the browser's language and on the Accept-Language header on HTTP requests. Defaults to `en-US`. |
| `platform` | `Win32` / `MacIntel` / `Linux armv8l` / `Linux armv7l` / `iPad` / `iPhone` / `Linux x86_64` | Configures the value `navigator.platform` should return. Defaults to `Win32` for desktop and `Linux armv8l` for mobile. Setting a custom platform will also affect the generated userAgent. |

