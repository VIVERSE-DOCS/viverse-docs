---
description: >-
  Learn how to start with VIVERSE Play SDK and implement Networking basics for
  your standalone PlayCanvas project
hidden: true
noIndex: true
---

# PlayCanvas Networking example: Part 01 - Basics

## About

Welcome to Part 01 of the VIVERSE Play SDK Networking tutorial for PlayCanvas! In this chapter we will covers all the basics to get you started with multiplayer functionality:

* Configure new PlayCanvas project for the VIVERSE SDKs
* Get to know the VIVERSE Play SDK and its Multiplayer Client
* \[...]

## Prerequisites

In order to use VIVERSE SDKs you need to create a World first and retrieve its App ID, which can be done with [VIVERSE Studio](https://studio.viverse.com/upload). This process is described in detail in [our documentation on VIVERSE Studio](https://app.gitbook.com/s/4pMiThqqrBzfvP8uy5am/publishing-with-your-viverse-account) — but for now you can simply create a new app and copy its App ID to get started.

> _**NOTE:** VIVERSE SDKs cannot be used with projects published via the_ [_PlayCanvas Create SDK extension_](https://docs.viverse.com/playcanvas-sdk/playcanvas-extension-setup)_, which do not have App IDs._

<div><figure><img src="../.gitbook/assets/cr1a (1).png" alt="" width="375"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/cr1b (1).png" alt="" width="375"><figcaption></figcaption></figure></div>

We're also assuming \[... Matchmaking SDK and room creation / joining]

## Step 1: Setup PlayCanvas project and add VIVERSE SDK

Let's create a new blank PlayCanvas project or use an already existing one. Go to the `SETTINGS` > `EXTERNAL SCRIPTS` and add a new script there: [`https://www.viverse.com/static-assets/viverse-sdk/index.umd.cjs`](https://www.viverse.com/static-assets/viverse-sdk/index.umd.cjs). This will ensure the VIVERSE SDK is loaded first and your PlayCanvas logic has full access to its functionality:

<figure><img src="../.gitbook/assets/cr2.png" alt="" width="375"><figcaption></figcaption></figure>

## Step 2: Create new scripts and initialize the SDK

For the purpose of this tutorial we will be using recently introduced [ESM scripts](https://developer.playcanvas.com/user-manual/scripting/fundamentals/esm-scripts/), although you can still follow it with the [Classic scripting](https://developer.playcanvas.com/user-manual/scripting/fundamentals/script-attributes/classic/) as well. Let's start with creating a new script called `main.mjs` and use built-in `initialize()` method to [instantiate Play SDK client](../matchmaking-and-networking-sdk.md#initialize-the-playclient-instance):

{% tabs %}
{% tab title="main.mjs" %}
```javascript
// @ts-nocheck
import { Script } from 'playcanvas';
import Client from './client.mjs';

export class Main extends Script
{
    static scriptName = 'Main';

    initialize ()
    {
        let client = new Client ();
    }
}
```
{% endtab %}

{% tab title="client.mjs" %}
```javascript
// @ts-nocheck
const { viverse } = globalThis;

export default class Client
{
    appId = '5snkdrvvv8';
    play = null;

    constructor ()
    {
        this.play = new viverse.Play ();
        console.log ('>>> Play SDK initialized:', this.play);
    }
}
```
{% endtab %}
{% endtabs %}

<figure><img src="../.gitbook/assets/mu1 (3).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
If you're new to PlayCanvas Editor and scripting system - we would strongly recommend consulting with official [PlayCanvas Scripting Guide](https://developer.playcanvas.com/user-manual/scripting/) before going any further. From now on we assume you're familiar with how scripts are added to the project, parsed and attached to Entities.
{% endhint %}

Congrats with a great start! Now if you launch your PlayCanvas project — you will see Play SDK client initialized and logged into the console. Please note that App ID is not required at this point, but we will definitely need it later!

## Step 3: Initialize Matchmaking Client and setup the Room

{% tabs %}
{% tab title="main.mjs" %}
```javascript
// @ts-nocheck
import { Script } from 'playcanvas';
import Client from './client.mjs';

export class Main extends Script
{
    static scriptName = 'Main';

    initialize ()
    {
        this.initClient ('Player A');
    }
    
    async initClient (username)
    {
        let client = new Client (username);
        await client.initialize ();
        return client;
    }
}
```
{% endtab %}

{% tab title="client.mjs" %}
```javascript
// @ts-nocheck
import { guid } from 'playcanvas';
const { viverse } = globalThis;

export default class Client
{
    appId = '5snkdrvvv8';
    username = null;
    play = null;
    matchmaking = null;

    constructor (username)
    {
        this.play = new viverse.Play ();
        this.username = username;
        
        this.initialize ();
    }
    
    async initialize ()
    {
        console.log ('>>> Initializing:', this.username);

        await this.initMatchmaking ();
        console.log ('>>> Matchmaking initialized');

        await this.createJoinRoom ();
        console.log ('>>> Joined the room');
    }
    
    //-----------------------------------------------------------------------------//
    //                               Initialization                                //
    //-----------------------------------------------------------------------------//
    
    async initMatchmaking ()
    {
        this.matchmaking = await this.play.newMatchmakingClient (this.appId);
        return new Promise (resolve => this.matchmaking.on ('onConnect', resolve));
    }

    async createJoinRoom ()
    {
        await this.matchmaking.setActor
        ({
            name: this.username,
            session_id: guid.create (),
            properties: {}
        });

        let {rooms} = await this.matchmaking.getAvailableRooms ();
        let {room} = rooms && rooms[0] ?
            await this.matchmaking.joinRoom (rooms[0].id) :
            await this.matchmaking.createRoom ({name: 'Room', mode: 'test', maxPlayers: 1000});
    }
}
```
{% endtab %}
{% endtabs %}

<figure><img src="../.gitbook/assets/mu2.png" alt=""><figcaption></figcaption></figure>

## Step 4: Initialize Multiplayer Client and subscribe to events

{% tabs %}
{% tab title="main.mjs" %}
```javascript
// @ts-nocheck
import { Script } from 'playcanvas';
import Client from './client.mjs';

export class Main extends Script
{
    static scriptName = 'Main';

    initialize ()
    {
         this.initClient ('Player A');
    }

    async initClient (username)
    {
        let client = new Client (username);
        await client.initialize ();
        return client;
    }
}
```
{% endtab %}

{% tab title="client.mjs" %}
```javascript
// @ts-nocheck
import { guid } from 'playcanvas';
const { viverse } = globalThis;

export default class Client
{
    appId = '5snkdrvvv8';
    username = null;
    play = null;
    matchmaking = null;
    multiplayer = null;

    constructor (username)
    {
        this.username = username;
        this.play = new viverse.Play ();
    }

    async initialize ()
    {
        console.log ('>>> Initializing:', this.username);

        await this.initMatchmaking ();
        console.log ('>>> Matchmaking initialized');

        await this.createJoinRoom ();
        console.log ('>>> Joined the room');

        await this.initMultiplayer ();
        console.log ('>>> Multiplayer initialized');

        this.multiplayer.general.onMessage (this.handleMessage.bind (this));
        this.multiplayer.actionsync.onCompetition (this.handleAction.bind (this));
        console.log ('>>> Client ready');
    }

    //----------------------------------------------------------------------------------//
    //                                 Initialiazation                                  //
    //----------------------------------------------------------------------------------//

    async initMatchmaking ()
    {
        this.matchmaking = await this.play.newMatchmakingClient (this.appId);
        return new Promise (resolve => this.matchmaking.on ('onConnect', resolve));
    }

    async createJoinRoom ()
    {
        await this.matchmaking.setActor
        ({
            name: this.username,
            session_id: guid.create (),
            properties: {}
        });

        let {rooms} = await this.matchmaking.getAvailableRooms ();
        let {room} = rooms && rooms[0] ?
            await this.matchmaking.joinRoom (rooms[0].id) :
            await this.matchmaking.createRoom ({name: 'Room', mode: 'test', maxPlayers: 1000});
    }

    async initMultiplayer ()
    {
        let room = this.matchmaking.getCurrentRoom ();
        this.multiplayer = await this.play.newMultiplayerClient (room.id, this.appId);
        await this.multiplayer.init ();
        return new Promise (resolve => this.multiplayer.onConnected (resolve));
    }

    //----------------------------------------------------------------------------------//
    //                                     Handlers                                     //
    //----------------------------------------------------------------------------------//

    handleMessage (data)
    {
        console.log (this.username, 'received message:', data);
    }

    handleAction (data)
    {
        console.log (this.username, 'received action:', data);
    }
}
```
{% endtab %}
{% endtabs %}

<figure><img src="../.gitbook/assets/mu3.png" alt=""><figcaption></figcaption></figure>

## Step 5: Final modifications and testing

```javascript
// @ts-nocheck
import { Script } from 'playcanvas';
import Client from './client.mjs';

export class Main extends Script
{
    static scriptName = 'Main';

    initialize ()
    {
        // We're exposing our initClient method globally
        // So we can test all functionality in browser console
        // Without involving PlayCanvas UI system at this point
        
        window.init = this.initClient.bind (this);
    }

    async initClient (username)
    {
        let client = new Client (username);
        await client.initialize ();
        return client;
    }
}
```
