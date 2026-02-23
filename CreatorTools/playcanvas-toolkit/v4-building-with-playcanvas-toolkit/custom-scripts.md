# Scripting

***





## Using Triggers to execute Custom Code

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
            this.dispatcher.getTriggerEventName ('portal.entered'),
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
            // Execute Action with `portal.toggle` name by pressing P on keyboard
            case pc.KEY_P:
                this.executeAction ('portal.toggle')
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







