---
layout: page
title: "Web API Applications"
category: controller
order: 2
---

## Web API

In daemon mode, you can add your own http endpoints to amorphic's internal server object.

On server startup, amorphic will look in your project directory
for an index file where you can export all of your application's routes (functionality that only gets triggered given a specific request pattern).

The location in which amorphic will look for this file is outlined below.

    ${project_name} > apps > ${application_name} > js > routers > index.js

The same pattern applies to defining middlewares (functionality that gets triggered on _any_
request) for your endpoints. Common examples of middlewares include capturing request metrics like request
 resolution time and payload size. The declaration of middlewares in an amorphic app follows the
 route definition pattern.

    ${project_name} > apps > ${application_name} > js > middlewares > index.js

### Example Endpoint

 Below is an example of setting up a route where we want to define some functionality along with
  the url structure reponsible for triggering it. These router functions should expect to receive an instance of an
express router object, add the routes, and return the updated router.

    {% highlight javascript %}
    function exampleEndpoint(expressRouter) {
        expressRouter.get('/example', exampleService);

        return expressRouter;
    }

    function exampleService (req, res) {
        res.status(200).send('test API endpoint OK');
    }

    module.exports = {
        exampleEndpoint: exampleEndpoint,
    };
    {% endhighlight %}

### Example Middleware

A similar pattern applies to setting up middlewares. In this example, we're imposing a request body size limit of
10 megabytes to all requests coming in.

    {% highlight javascript %}
    let express = require('express');

    function exampleMiddleware(expressRouter) {
        expressRouter.use(express.json({
            limit: '10mb'
        }));

        return expressRouter;
    }

    module.exports = {
        exampleMiddleware: exampleMiddleware
    };
    {% endhighlight %}

NOTE: In the above examples, all of the application's endpoints and functionality
 are implemented within the index file. If your application is any larger than POC scale, you should
logically separate out your functionality into different files and import them
into the index file. This way you can organize code in a way that makes sense
for your application.

Also, on each call you will need to update the database as no state is maintained between server calls.
If you need state, you will need to manage it on your own.