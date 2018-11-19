---
layout: page
title: "Web API Applications"
category: controller
date: 2013-06-06 08:55:36
order: 2
---

# Web API

In daemon mode (beta), you can add your own http endpoints to amorphic's internal server object.

On server startup, amorphic will look in your project directory
for an index file where you can export all of your application's routes. Routes to us
means functionality that gets triggered only given a request pattern
e.g. URL structure or http request type (get, post, put, etc.).

    ${project_name}> apps > ${application_name} > js > routers > index.js

The same pattern applies to defining middlewares, functionality that gets triggered on _any_
request to your endpoints. Common examples of middlewares include capturing request metrics like request
 resolution time and size of payload. The declaration of middlewares in an amorphic app follows the
 route definition pattern.

    ${project_name}> apps > ${application_name} > js > middlwares > index.js

### Example

 Let's set up an example endpoint. We want to define the functionality that's supposed to get triggered,
 and the http url structure reponsible for triggering it. These functions should expect to receive an instance of an
express router object, add the routes, and return the updated router.

    function firstEndpoint(expressRouter) {
        expressRouter.get('/example', exampleService);

        return expressRouter;
    }

    function exampleService (req, res) {
        res.status(200).send('test API endpoint OK');
    }

    module.exports = {
        firstEndpoint: firstEndpoint,
    };

NOTE: In this above example, all of the application's endpoints and functionality
 are implemented within the index file. If your application is any larger than POC scale, you should
logically separate out your functionality into different files and import them
into the index file. This way you can organize your code in a way that makes sense
for your application, while the index file serves as an entrypoint.

On each call you would need to update the database using Persistence calls.

No state is maintained between calls. If you need state you need to manage it on your own.