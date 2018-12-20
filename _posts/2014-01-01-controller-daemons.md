---
layout: page
title: "Batch Applications (Daemon Mode)"
category: controller
order: 1
---

### Daemons (background tasks)

While running this mode in amorphic, browser sessions are not managed by amorphic. To do that include this in your config.json file:

    'isDaemon': 'true',

Your controller will be created immediately upon startup and the serverInit function will be called for it to get started.

When `templateMode` is set to `'typescript'`:

    {% highlight javascript %}
    import { supertypeClass } from 'amorphic';

    @supertypeClass
    export class Controller {
        serverInit() {
            setInverval(() => this.doBatchTask(), 5000);
        }
        doBatchTask() {
            console.log('Place your batch task here');
        }
    }
    {% endhighlight %}

When `templateMode` is **not** set to `'typescript'`:

    {% highlight javascript %}
    module.exports.controller = function (objectTemplate, getTemplate) {
        var BaseController = getTemplate('./baseController.js').BaseController;

        Controller = BaseController.extend({
            serverInit: function () {
                setInterval(() => {
                    this.doBatchTask();
                }, 5000);
             },
             doBatchTask: function () {
                 console.log("I'm a batch task");
             }
         });

         return {Controller: Controller};
     }
     {% endhighlight %}

 At present daemons are executed on the same thread as the main application when `templateMode` is **not** set to `'typescript'`.

 The thinking is that in a production environment you would probably deploy them as separate node.js processes. You can control what gets started by overriding the applications property of your config.json with an environment variable or node.js startup parameter.