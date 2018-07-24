---
layout: page
title: "Setup"
category: config
date: 2014-01-01 00:00:00
order: 0
---


### Installation

There are two ways to get started:

* Fork the ticket demo
* install into an existing project

The ticket demo has some ready examples of complex relationships in the model so this can be a good bet for those who want to delve in quickly.  If you already know amorphic or want to take a more methodical approach or already have some existing code then installing Amorphic as an npm is best:

    npm install amorphic

Amorphic is based on connect middleware. It creates a connect applicaiton and listens on it.  All you need in your app.js is to call amorphic:

    {% highlight javascript %}
    let rootDirectory = __dirname;
    require('amorphic').listen(rootDirectory, sessionStore, preSessionCallback, postSessionCallback);
    {% endhighlight %}

Parameters are:

* `rootDirectory` (string) - which should always be __dirname
* `sessionStore` - a session store object which defaults to MemoryStore
* `preSessionCallback` (function) - a callback that passes in the connect handler returned from `connect()` **before** any `.use` events are attached.  This is where any special requirements for static files or other requests that don't need the `bodyParser` or sessions can be attached via `.use`

    {% highlight javascript %}
    function preSessionInject(app) {
        app.use(function(req, res, next) {
            // do work here
            next();
        })
    }
    {% endhighlight %}

* `postSessionCallback` (function) - a callback that passes in the connect handler returned from `connect()` after `app.session()` and the body parser have been called such that requests that require the session can be attached.

    {% highlight javascript %}
    function postSessionInject(app) {
        app.use(function(req, res, next) {
            // do work here
            next();
        })
    }
    {% endhighlight %}

Note that `preSessionCallback` and `postSessionCallback` are not required in an Amorphic application because communication with the server is typically handled by object model methods declared live on the server.  This communication is handled automatically.

### Global config.json

In the root of your project there is a `config.json` file which represents the "global" configuration for all applications:

* `applications` <span style="color: red">required</span> - list your applications and their root directories here

* `application` <span style="color: red">required</span> - the default application

* `conflictMode` - Whether to data synchronization vs only message synchronization is enabled.  Defaults to `'soft'`.  Every data value transmitted when synchronizing between the browser and server is sent in two parts - the expected current value and the new value.  If the expected current value is incorrect a warning is generated (conflictMode 'soft') or an error is generated (conflictMode 'hard')

* `compressSession` (boolean) - if set to true session data is compressed before being serialized.  This makes storage smaller but adds time to each server call

* `compressXHR` (boolean) - if set to true the server responses are compressed

* `sourceMode` (string)
    * `'prod'` - source files served to the client are minified
    * `'debug'` - source files served to the client are not minified
    * `'webpack'` - amorphic will not serve source files to the client

* `templateMode` (string)
    * `'auto'` - circular references are handled by the framework with this option
    * `'typescript'` - framework will process javascript templates that are compiled from typescript

* `port` (number) - the port you want to listen on

* `sessionSeconds` (number) - how long before a session expires (in seconds)

* `objectCacheSeconds` (number) - how long to keep your objects cached

* `sessionSecret` (string) - a random string for hashing sessions

Anything you specify in config.json can also be specified as a starting parameter to node.js


Example:

    {% highlight JSON %}
    {
        "port": 3001,
        "sessionSeconds" : 3600,
        "objectCacheSeconds" : 600,
        "sessionSecret" : "Ey0veeljPcLsHitdxCG2",
        "applications" : {"ticket": "apps/ticket"},
        "application"  : "ticket"
    }
    {% endhighlight %}
