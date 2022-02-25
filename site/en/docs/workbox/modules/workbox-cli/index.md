---
layout: 'layouts/doc-post.njk'
title: workbox-cli
date: 2017-11-27
updated: 2020-01-17
description: >
  Generate a service worker, inject a precache manifest, or create a local copy the Workbox libraries from the command line.
---

The Workbox command line interface (contained in the
`workbox-cli` package) consists of a Node.js program called workbox that
can be run from a Windows, macOS, of UNIX-compatible command line
environment. Under the hood, workbox-cli wraps the workbox-build module,
and provides an easy way of integrating Workbox into a command line build
process, with flexible configurations.

## Install the CLI

To install the CLI with Node, run the following command in your terminal:

```shell
npm install workbox-cli --global
```

## CLI Modes

The CLI has four different modes:

- [`wizard`](#wizard): A step-by-step guide to set up Workbox for your project.
- [`generateSW`](#generatesw): Generates a complete service worker for you.
- [`injectManifest`](#injectmanifest): Injects the assets to precache into your project.
- [`copyLibraries`](#copylibraries): Copy the Workbox libraries into a directory.

### `wizard`

The Workbox wizard asks a series of questions about your local directory
setup and which files you want precached. Your answers are used to
generate a configuration file which can then be used when running in
`generateSW` mode.

Most developers will only need to run workbox wizard once, and you're free
to manually customize the initial configuration file that's generated,
using any of the supported build configuration options.

To start the wizard run:

```shell
npx workbox wizard
```

{% Img src="image/jL3OLOhcWUQDnR4XjewLBx4e3PC3/WCi92GCC5iwZPyE1fruQ.png", alt="Screenshot of Workbox CLI's wizard", width="800", height="480" %}

### `generateSW`

You can use the Workbox CLI to generate a complete service worker using
a configuration file (like the file generated by the wizard.)

Just run the following command:

```shell
npx workbox generateSW path/to/config.js
```

Developers who are happy with Workbox's built-in precaching and runtime caching
capabilities, and don't need to customize their service worker's behavior
are recommended to use `generateSW` mode.

#### When to use `generateSW`

- You want to precache files.
- You have simple runtime configuration needs (e.g. the configuration allows you to define routes and strategies).

#### When NOT to use `generateSW`

- You want to use other Service Worker features (i.e. Web Push).
- You want to import additional scripts or add additional logic.

### `injectManifest`

For developers who want more control of their final service worker file
can use the `injectManifest` mode. This mode assumes that you have an
existing service worker file (the location of which is specified in config.js).

When `workbox injectManifest` is run, it looks for a specific string
(`precacheAndRoute(self.__WB_MANIFEST)` by default) in your source
service worker file. It replaces the empty array with a list of
URLs to precache and writes the service worker file to its
destination location, based on the configuration options in `config.js`.
The rest of the code in your source service worker is left untouched.

You can use Workbox in this mode like so:

```shell
npx workbox injectManifest path/to/config.js
```

#### When to use `injectManifest`

You want more control over your service worker.
You want to precache files.
You have more complex needs in terms of routing.
You would like to use your service worker with other API's (e.g. Web Push).

#### When NOT to use `injectManifest`

You want the easiest path to adding a service worker to your site.

### `copyLibraries`

This mode is helpful if you would like to use `injectManifest` and would
like to use the Workbox library files hosted on your own origin instead
of using the CDN.

You just need to run it with a path to write the files to:

```shell
npx workbox copyLibraries third_party/workbox/
```

## Build Process Integration

### Why Does Workbox need to Integrate with My Build Process?

The Workbox project contains a number of libraries that work together to
power your web app's
[service worker](https://developers.google.com/web/fundamentals/primers/service-workers/). In order to
use those libraries effectively, Workbox needs to be integrated into your
web app's build process. This ensures that your service worker is able to
efficiently precache all of your web app's critical content, and keep that
content up to date.

### Is `workbox-cli` the Right Choice for My Build Process?

If you have an existing build process that is based entirely on
[npm scripts](https://docs.npmjs.com/misc/scripts),
then the `workbox-cli` is a good choice.

If you're currently using [webpack](https://webpack.js.org/) as your build
tool, then using the [workbox-webback-plugin](/docs/workbox/modules/workbox-webpack-plugin)
is a better choice.

If you're currently using [Gulp](https://gulpjs.com/),
[Grunt](https://gruntjs.com/), or some other Node.js-based build tool,
then integrating [workbox-build](/docs/workbox/modules/workbox-build) into your build script
is a better choice.

If you don't have a build process at all, then you should come up with one
before using Workbox to precache any of your assets. Trying
to remember to run workbox-cli manually can be error-prone, and forgetting
to run it can lead to stale content being served to returning visitors.

### Setup and Configuration

After [installing](#install-the-cli) `workbox-cli` as a development
dependency for your local project, you can add the call to `workbox` at
the end of your existing build process's npm script:

From package.json:

```json
{
  "scripts": {
    "build": "my-build-script && workbox <mode> <path/to/config.js>"
  }
}
```

Replace `<mode>` with `generateSW` or `injectManifest` (depending
on your use case) and `<path/to/config.js>` with the path to
your configuration options. Your configuration might have been created
automatically by `workbox wizard` or tweaked manually.

## Configuration

### Options used by `generateSW`

A full set of configuration options can be found on
[this reference page](/docs/workbox/reference/workbox-build/#method-generateSW).

### Options used by `injectManifest`

A full set of configuration options can be found on
[this reference page](/docs/workbox/reference/workbox-build/#method-injectManifest).