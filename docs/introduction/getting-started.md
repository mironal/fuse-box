# Getting started

Before we proceed, let's make sure of that we are using NodeJS 8.2+ installed

## Assumptions

* What is bundling?
You are familiar with the term bundling, and you know why it's required
* What is npm?
You know what is NPM and how it works


## Hop on board

* Create `fuse.js`
* Define your producer
* Dev options
* bundle instructions

The initial configuration is quite simple.

```js
const { FuseBox, WebIndexPlugin } = require("fuse-box");
const fuse = FuseBox.init({
    homeDir : "src",
    target : 'browser@es6',
    output : "dist/$name.js",
    plugins : [
        WebIndexPlugin()
    ]
})
fuse.dev(); // launch http server
fuse.bundle("app").instructions(" > index.ts").hmr().watch()
fuse.run();
```

This will get you started pretty much instantly. `index.html` will be created automatically.

In this example we are using typescript, if you don't have `tsconfig.json` FuseBox creates one automatically for you.

## Choosing correct target

It's important to setup correct script and platform target for your bundle.

```js
const fuse = FuseBox.init({
    target : 'browser@es6',
    homeDir : "src",
    output : "dist/$name.js"
});
```

Let's take a look at the first option here - `target`. It can be `browser` or `server` you can also set `universal`, however if you want to have platform optimised bundles you should choose either `server` or `browser`. You can control the script target by adding `@` symbol. For example: `browser@es5` or `server@esnext`

FuseBox detects the actual target of files, so when you choose `es5` and a script is using `es6` feature, TypeScript transpiler will transpile it down.



## Start with TypeScript

You won't need any additional configuration to start working with typescript.

* Make sure to choose the correct [target](#choosing-correct-target)
* `tsconfig.json` will be created automatically if none was found

## Start with JavaScript

You are not forced to used typescript. However you should still have it installed, as typescript is not just a powerful tool for typechecking - it's also a very [powerful transpiler](/page/configuration#usetypescriptcompiler) too

```js
const fuse = FuseBox.init({
    homeDir : "src",
    target : 'browser@es6',
    output : "dist/$name.js",
    useTypescriptCompiler : true,
});
fuse.bundle("app")
    .instructions(" > index.js").hmr().watch()
fuse.run();
```

Use `useTypescriptCompiler` option and you won't need babel at all. But keep in mind synthetic default imports.

Correct:
```js
import * as React from "react"
```
NOT correct:

```js
import React from "react" // <-- not correct ()
```

It will work with [polyfillNonStandardDefaultUsage](/page/configuration#polyfillnonstandarddefaultusage). However, it's not recommended adding this option as it adds additional overhead to your runtime.



## Start with CSS

Adding CSS support it very easy.

```js
const { FuseBox, SassPlugin, CSSPlugin } = require("fuse-box");
const fuse = FuseBox.init({
    target : 'browser@es6',
    homeDir : "src",
    output : "dist/$name.js",
    plugins : [
        [ SassPlugin(), CSSPlugin() ]
    ]
});
```

But before you start experimenting, you should read [this](/page/about-plugins) section in order to understand how plugins work. The common mistake made by rookies is not grouping (chaining) the plugins.

```js
plugins : [
    [ SassPlugin(), CSSPlugin() ]
]
```

If you are planning on using CSSModules, here is a little trick for TypeScript that will make it stop complaining about typings:

```js
// for default imports
// e.g import style from "./main.css"
declare module "*.css" {
    const content: string;
    export default content;
}
// for regular imports
// e.g import * as styles from "./main.css"
declare module "*.css" {
    const content: string;
    export = content;
}
```

## Start with server

In order to achieve the best performance you should choose `server@esnext` target
```js
const fuse = FuseBox.init({
    homeDir : "src",
    target : 'server@esnext',
    output : "dist/$name.js"
});
fuse.bundle("app")
    .completed(proc => proc.start)
    .instructions(" > index.ts").hmr().watch()
fuse.run();
```

FuseBox can take care of launching your server bundle. `.completed(proc => proc.start)` block will start/restart the process automatically.


## Making production builds

Production builds are made using the [Quantum](/page/quantum) plugin.

```js
plugins: [
    this.isProduction && QuantumPlugin({
        uglify: true,
        treeshake : true,
        bakeApiIntoBundle: "app"
    })
]
```

Before going there, read up [how to deal with the task runner](/page/getting-started-with-sparky) as it will greatly simplify the workflow.

## Diving deeper

Once you are familiar with the concepts, you can start with [Sparky](/page/sparky) - A task runner that makes you life easier.

You can find plenty of example [here](https://github.com/fuse-box/fuse-box-examples/tree/master/examples)
