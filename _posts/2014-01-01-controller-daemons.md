---
layout: page
title: "Batch Applications (Daemon Mode)"
category: controller
date: 2013-06-06 08:55:36
order: 1
---

### Daemons (background tasks)

While running this mode in amorphic, browser sessions are not managed by amorphic. To do that include this in your config.json file:

       'isDaemon': 'true',

Your controller will be created immediately upon startup and the serverInit function will be called for it to get started.

When `templateMode` is set to `'typescript'`:

    {% highlight javascript %}
    import {supertypeClass} from 'amorphic';

    @supertypeClass
    export class Controller {
        serverInit() {
            setInverval(() => this.interval(), 5000);
        }
        interval() {
            console.log('Place your batch task here');
        }
    }
    {% endhighlight %}

When `templateMode` is **not** set to `'typescript'`:

    {% highlight javascript %}
     module.exports.controller = function (objectTemplate, getTemplate)
     {
        var BaseController = getTemplate('./baseController.js').BaseController;

         Controller = BaseController.extend(
        {
             serverInit: function () {
                 setInterval(function () {this.interval()}.bind(this), 5000);
             },
             interval: function () {
                 console.log("I'm a batch task");
             }

         });

         return {Controller: Controller};
     }
     {% endhighlight %}

 You can also listen on any port at this point but you don't have access to the main server that is listening for http requests from the browser.

 At present daemons are executed on the same thread as the main application when `templateMode` is **not** set to `'typescript'`.  The thinking is that in a production environment you would probably deploy them separately on separate node.js processes.  You can control what gets started by overriding the applications property of your config.json with an environment variable or node.js startup parameter.