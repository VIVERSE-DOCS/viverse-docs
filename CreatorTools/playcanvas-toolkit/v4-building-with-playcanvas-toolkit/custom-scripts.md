---
description: Learn how to link your custom code to Triggers and Actions
---

# Custom Scripts

***

## Introduction

While VIVERSE Toolkit provides a huge range of no-code tools covering a lot of typical use cases, it doesn't stop you from writing [Custom Scripts](https://developer.playcanvas.com/user-manual/scripting/) and using full [PlayCanvas API](https://api.playcanvas.com/engine/) to your advantage! Below is a collection of recipes designed to help you create a bridge between Toolkit's Framework and PlayCanvas Scripting API.

## Using Triggers to execute Custom Code

One of the most useful integrations is to have a Custom Script that can execute some function when particular Trigger is activated. Here is how it can be done:

* Create a new [Trigger](triggers.md) Entity of desired type, or use an already existing one. Make sure it has unique `Name` since it will be called inside our Custom Script
* Create a new .mjs Script, parse it and attach to some Entity in your Scene. **It doesn't have to be** our Trigger Entity — any other Entity will do
* Use the boilerplate below to link Custom Script function to your Trigger. Replace `some.trigger.name` in our example with your own unique Trigger's `Name`&#x20;
* Add your custom code inside `onTriggerActivated ()` handler, then launch your Scene and test!

{% tabs %}
{% tab title="script.mjs" %}
```javascript
// @ts-nocheck
import { Script } from 'playcanvas';
import { TriggerDispatcher } from '../.viverse/toolkit/extension.mjs';

export class TriggerHandler extends Script
{
    static scriptName = 'Trigger Handler';

    initialize ()
    {
        // Retrieve singleton instance of TriggerDispatcher
        this.dispatcher = TriggerDispatcher.getInstance ();
        
        // Use .on or .once to subscribe to your custom trigger events
        // Note how full event id is retrieved via .getTriggerEventName first
        this.dispatcher.on
        (
            this.dispatcher.getTriggerEventName ('some.trigger.name'),
            this.onTriggerActivated,
            this
        );
    }

    // Your custom code can be placed here
    onTriggerActivated ()
    {
        alert ('Custom code executed!');
    }
}
```
{% endtab %}
{% endtabs %}

## Executing Actions from Custom Code

Another widely used integration is to execute some Action from inside your Custom Script, under certain conditions — for example when user pressed some keyboard key. Here is how it can be done:

* Create a new [Action](actions.md) Entity of desired type, or use an already existing one. Make sure it has unique `Name` since it will be called inside our Custom Script
* Create a new .mjs Script, parse it and attach to some Entity in your Scene. Unlike Trigger example above, **this script has to be attached** to our Action Entity in order to work prolery
* Use the boilerplate below to execute this Action inside your Custom Script function. Replace `some.action.name` in our example with your own unique Action's `Name`&#x20;
* Launch your Scene and test! Feel free to replace our keyboard activator by any other logic of your choice, and just keep `executeAction`&#x20;

{% tabs %}
{% tab title="script.mjs" %}
```javascript
// @ts-nocheck
import { Script } from 'playcanvas';

export class ActionExec extends Script
{
    static scriptName = 'Action Exec';

    initialize ()
    {
        // Subscribe to keyboard event to execute your custom code
        this.app.keyboard.on ('keydown', this.onKeyDown, this);
    }

    // Your custom code can be placed here
    onKeyDown (event)
    {
        switch (event.key)
        {
            // Execute Action with `some.action.name` name by pressing P on keyboard
            case pc.KEY_P:
                this.executeAction ('some.action.name')
                break;
        }
    }
    
    // Helper to execute desired Action found in `viverseAction` script instance
    executeAction (name)
    {
        const script = this.entity.findScript ('viverseAction');
        const action = script?.actions.find (action => action.activation === name);
        const execution = action && script?._getExecution (action);
        if (execution) execution (action);
    }
}
```
{% endtab %}
{% endtabs %}
