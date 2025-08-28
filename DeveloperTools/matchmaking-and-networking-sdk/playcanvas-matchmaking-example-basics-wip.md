---
description: >-
  Learn how to start with VIVERSE Play SDK and implement basic Matchmaking for
  your standalone PlayCanvas project while experimenting with screen-based UI
  system and neatly organized state
hidden: true
noIndex: true
---

# PlayCanvas Matchmaking example: Basics (WIP)

### Pre-requisite: Create a World and App ID in VIVERSE Studio

In order to use VIVERSE SDKs you would need to create a World first and retrieve its App ID, which can be done with [VIVERSE Studio](https://studio.viverse.com/upload). This process is described in detail in [our documentation on VIVERSE Studio](https://app.gitbook.com/s/4pMiThqqrBzfvP8uy5am/publishing-with-your-viverse-account) — but for now you can simply create a new app and copy its App ID to get started.

> _**NOTE:** VIVERSE SDKs cannot be used with projects published via the PlayCanvas Create SDK extension, which do not have App IDs._

### Step 1: Create a new PlayCanvas project and add the VIVERSE SDK as an external script

Let's create a new blank PlayCanvas project or use an already created one that you have. Go to the SETTINGS > EXTERNAL SCRIPTS and add a new script there: [`https://www.viverse.com/static-assets/viverse-sdk/index.umd.cjs`](https://www.viverse.com/static-assets/viverse-sdk/index.umd.cjs). This will ensure the VIVERSE SDK is loaded first and your PlayCanvas logic has full access to its functionality.

### Step 2: Create a new script and initialize the SDK

For the purpose of this tutorial we will be using recently introduced [ESM scripts](https://developer.playcanvas.com/user-manual/scripting/fundamentals/esm-scripts/), although you can still follow it with the [Classic scripting](https://developer.playcanvas.com/user-manual/scripting/fundamentals/script-attributes/classic/) as well. Let's start with creating a new script called `main.mjs` and use built-in `initialize ()` method to instantiate Play SDK client:

```javascript
// @ts-nocheck
import { Script, Entity, guid } from 'playcanvas';

// If Viverse SDK is set up correctly
// `viverse`should be available on window / globalThis
const { viverse } = globalThis;

export class Main extends Script
{
    static scriptName = 'Main';
    
    initialize ()
    {
        this.appId = 'ajhzug2zwb'; // replace with your App ID
        this.playClient = new viverse.Play ();
        
        console.log (this.playClient);
    }
}
```

{% hint style="info" %}
If you're new to PlayCanvas Editor and scripting system - we would strongly recommend consulting with official [PlayCanvas Scripting Guide](https://developer.playcanvas.com/user-manual/scripting/) before going any further. From now on we assume you're familiar with how scripts are added to the project, parsed and attached to Entities.
{% endhint %}

Congrats with a great start! Now if you launch your PlayCanvas project — you will see Play SDK client initialized and logged into the console.

### Step 3: Create a new script and initialize the SDK

The Play SDK client is just a starting point and doesn't do anything on its own. In order to make use of its matchmaking features we would need to initialize Matchmaking client first and associate some Actor with our user:

```javascript
// @ts-nocheck
import { Script, Entity, guid } from 'playcanvas';
const { viverse } = globalThis;

export class Main extends Script
{
    static scriptName = 'Main';
    
    initialize ()
    {
        this.appId = 'ajhzug2zwb'; // replace with your App ID
        this.playClient = new viverse.Play ();
        
        this.initMatchClient ();
    }
    
    async initMatchClient ()
    {
        this.matchClient = await this.playClient.newMatchmakingClient (this.appId);
        this.matchClient.on ('onConnect', async () =>
        {
            await this.matchClient.setActor
            ({
                name: 'Some Username', // non-unique username
                session_id: 'abc123', // some id string unique to your user
                properties: {a: 1, b: true} // additional data if any
            });
            
            console.log (this.matchClient);
            console.log (this.matchClient.currentActor);
        });
    }
}
```

There are a few gotchas to keep an eye for:

* Play SDK doesn't require users to be logged in with VIVERSE
* Trying to create an Actor immediately after Matchmaking Client instantiation will result in web socket error. That's why we need to subscribe to `onConnect` event - only then our Client is considered ready
* You don't have to create an Actor right after the Client is connected. But as you see later, it still has to be done before user attempts creating or joining the Room
* From now on we're going to use async / await heavily. If you want to refresh... (TODO finish this)
