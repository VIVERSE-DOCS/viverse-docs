---
description: >-
  Learn how to integrate VIVERSE Play SDK Networking into your standalone
  PlayCanvas project using [...].
hidden: true
noIndex: true
---

# PlayCanvas Networking example: Part 02 - Advanced

## About

Welcome to Part 02 of the VIVERSE Play SDK Networking tutorial for PlayCanvas! In this chapter we will cover multiple advanced topics:

* \[...]

## Prerequisites

This tutorial assumes you've completed \[Part 01] and already familiar with the basics of [VIVERSE Play SDK](../matchmaking-and-networking-sdk.md) / \[Networking functionality]. Please feel free to revisit those if you need a quick recap!

In the second part, we'll focus on \[...]. You can follow this tutorial by forking a dedicated \[PlayCanvas Project] with all the code and assets included.

## Step 1

\[... Intro about architecture design and state handling]

Let's start by setting up a \[...main frame of our application]:

* `app.mjs` : defines App class which would serve as an entry point of our application and create a new State instance. App is extending PlayCanvas Script class and should be attached to some entity to work properly
* `state.mjs` : defines State class which would be responsible for managing global state of our application. It's based around standard Map, but implements a simple deep cloning mechanism where it's applicable, to mitigate possible issues when various scripts are mutating State objects after setting / getting them

{% tabs %}
{% tab title="app.mjs" %}
```javascript
// @ts-nocheck
import { Script } from 'playcanvas';
import State from './state.mjs';


export class App extends Script
{
    static scriptName = 'App';

    initialize ()
    {
        this.app.state = new State ();
    }

    postUpdate (dt)
    {
        this.app.state.log ();
    }
}
```
{% endtab %}

{% tab title="state.mjs" %}
```javascript
// @ts-nocheck
export default class State extends Map
{
    constructor (...args)
    {
        return super (...args);
    }

    set (key, value)
    {
        if (value === undefined || value === null)
            return super.set (key, value);
        else if (typeof value.clone === 'function')
            return super.set (key, value.clone ()); // Used for Vec2, Vec3, etc
        else if (value instanceof Map)
            return super.set (key, value); // Passing Maps as is, without cloning
        else if (Array.isArray (value))
            return super.set (key, [...value]); // Deep cloning arrays on write
        else if (value instanceof Object)
            return super.set (key, {...value}); // Deep cloning objects on write
        else
            return super.set (key, value);
    }

    get (key)
    {
        let value = super.get (key);

        if (value === undefined || value === null)
            return value;
        else if (typeof value.clone === 'function')
            return value.clone (); // Used for Vec2, Vec3, Entity, etc
        else if (value instanceof Map)
            return value; // Returning Maps as is, without cloning
        else if (Array.isArray (value))
            return [...value]; // Deep cloning arrays on read
        else if (value instanceof Object)
            return {...value}; // Deep cloning objects on ready
        else
            return value;
    }

    log ()
    {
        for (let [key, value] of this.entries ())
            if (value instanceof Map)
                window.sessionStorage.setItem (key, JSON.stringify ([...value.values ()]))
            else
                window.sessionStorage.setItem (key, JSON.stringify (value));
    }
}
```
{% endtab %}
{% endtabs %}

\[...Testing via pc.app.state.set (...) and pc.app.state.get (...)]

## Step 2

Now let's borrow a `client.mjs` script which we created in Part 01, and refactor it to fit our new architecture. The new Client will have 4 internal variables exposed to other scripts via our global application State:

* `status` — indicates current initialization progress and can be `init` -> `matchmaking` -> `room` -> `multiplayer` -> `ready`
* `actor` — data related to current user's Actor. Will be updated internally by Matchmaking client (no need to listen to special events for this)
* `room` — data related to current Room and the list of Actors in it. Also will be updated by Matchmaking client automatically
* `snapshot` — a Map collecting all relevant information about entities in the current Room, both local and remote. We'll expand on that a bit later

{% tabs %}
{% tab title="client.mjs" %}
```javascript
// @ts-nocheck
import { Script, guid } from 'playcanvas';
const { viverse } = globalThis;


export class Client extends Script
{
    static scriptName = 'Client';

    initialize ()
    {
        this.appId = '5snkdrvvv8';
        this.play = new viverse.Play ();

        this.data =
        {
            status: 'init',
            actor: null,
            room: null,
            snapshot: new Map ()
        };
    }

    postInitialize ()
    {
        this.app.state.set ('client.status', this.data.status);
        this.app.state.set ('client.actor', this.data.actor);
        this.app.state.set ('client.room', this.data.room);
        this.app.state.set ('client.snapshot', this.data.snapshot);

        this.initClient ();
    }

    async initClient ()
    {
        this.data.status = 'matchmaking';
        await this.initMatchmaking ();

        this.data.status = 'room';
        await this.createJoinRoom ();

        this.data.status = 'multiplayer';
        await this.initMultiplayer ();

        this.multiplayer.general.onMessage (this.handleMessageIn.bind (this));
        this.app.on ('client:send', this.handleMessageOut, this);

        this.data.status = 'ready';
    }

    //----------------------------------------------------------------------------------//
    //                                     Handlers                                     //
    //----------------------------------------------------------------------------------//

    handleMessageIn (data)
    {
        if (data.id)
            this.data.snapshot.set (data.id, data);
    }

    handleMessageOut (data)
    {
        if (data.id)
        {
            this.multiplayer.general.sendMessage (data);
            this.data.snapshot.set (data.id, data);
        }
    }

    //----------------------------------------------------------------------------------//
    //                                      Update                                      //
    //----------------------------------------------------------------------------------//

    update (dt)
    {
        this.data.actor = this.matchmaking?.getCurrentActor ();
        this.data.room = this.matchmaking?.getCurrentRoom ();

        let ids = this.data.room?.actors.map (actor => actor.session_id) || [];
        for (let [key, data] of this.data.snapshot.entries ())
            if (!ids.includes (data.owner))
                this.data.snapshot.delete (key);

        this.app.state.set ('client.status', this.data.status);
        this.app.state.set ('client.actor', this.data.actor);
        this.app.state.set ('client.room', this.data.room);
        this.app.state.set ('client.snapshot', this.data.snapshot);
    }

    //----------------------------------------------------------------------------------//
    //                                      Utils                                       //
    //----------------------------------------------------------------------------------//

    async initMatchmaking ()
    {
        this.matchmaking = await this.play.newMatchmakingClient (this.appId);
        return new Promise (resolve => this.matchmaking.on ('onConnect', resolve));
    }

    async createJoinRoom () an inter
    {
        await this.matchmaking.setActor
        ({
            name: '',
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
}
```
{% endtab %}
{% endtabs %}

Let's see what's happening in our new refactored Client:

* We still have original initialization routine borrowed from the previous version, but now it's updating `status` each time the Client reaches certain initialization stage
* At the same time, we're now extending it from PlayCanvas Script class, so we can rely on built-in `update ()` method to write all necessary internal variables to our global State, where they will be processed by other scripts down the line (which we introduce later in this tutorial)
* And finally, we're setting up handlers for incoming and outgoing messages, tightly integrating them with Snapshot updates. So each time a new message arrives from remote peer — its data is merged into corresponding Snapshot entry, under `data.id` key. And each time some local entity broadcasts a message to remote peers via our Client — the message's `data` is also merged into corresponding entry as well. This way the `snapshot` variable always stores the latest data about all multiplayer entities in the current Room — to its best ability.

\[...Showing Client states in session storage]

<figure><img src="../.gitbook/assets/mu21.gif" alt=""><figcaption></figcaption></figure>

## Step 3

{% tabs %}
{% tab title="player.mjs" %}
```javascript
// @ts-nocheck
import { Script, Vec2 } from 'playcanvas';
import { KEY_UP, KEY_DOWN, KEY_LEFT, KEY_RIGHT} from 'playcanvas';
import { KEY_W, KEY_S, KEY_A, KEY_D } from 'playcanvas';


export default class Player extends Script
{
    static scriptName = 'Player';

    initialize () {}

    //----------------------------------------------------------------------------------//
    //                                      Update                                      //
    //----------------------------------------------------------------------------------//

    update (dt)
    {
        let inputdir = this.processInput ();
        let movedir = new pc.Vec3 (inputdir.x, 0, -inputdir.y);
        let mass = this.entity.rigidbody.mass;
        let force = movedir.clone ().mulScalar (20 * mass);
        this.entity.rigidbody.applyForce (force.x, force.y, force.z);
    }

    //----------------------------------------------------------------------------------//
    //                                      Utils                                       //
    //----------------------------------------------------------------------------------//

    processInput ()
    {
        let dir = new Vec2 ();

        if (this.app.keyboard.isPressed (KEY_W) || this.app.keyboard.isPressed (KEY_UP))
            dir.y += 1;
        if (this.app.keyboard.isPressed (KEY_S) || this.app.keyboard.isPressed (KEY_DOWN))
            dir.y -= 1;
        if (this.app.keyboard.isPressed (KEY_A) || this.app.keyboard.isPressed (KEY_LEFT))
            dir.x -= 1;
        if (this.app.keyboard.isPressed (KEY_D) || this.app.keyboard.isPressed (KEY_RIGHT))
            dir.x += 1;

        return dir.normalize ();
    }
}
```
{% endtab %}
{% endtabs %}

\[...Testing local player movement]

## Step 4

{% tabs %}
{% tab title="game.mjs" %}
```javascript
// @ts-nocheck
import { Script, Asset, guid } from 'playcanvas';


/** @interface */ class Templates
{
    /** @title Player @type {Asset} @resource template */ player;
    /**/
}

export class Game extends Script
{
    static scriptName = 'Game';

    /** @attribute @title Templates @type {Templates} */ templates;

    initialize () {}

    //----------------------------------------------------------------------------------//
    //                                      Update                                      //
    //----------------------------------------------------------------------------------//

    update (dt)
    {
        let ready = this.app.state.get ('client.ready');
        let actor = this.app.state.get ('client.actor');
        let snapshot = this.app.state.get ('client.snapshot');

        if (!ready)
            return;

        for (let [key, data] of snapshot.entries ())
            if (!this.entity.findByName (key))
                this.createEntity (data);

        for (let entity of this.entity.children)
            if (!snapshot.get (entity.name))
                this.destroyEntity (entity);

        let player = this.find (snapshot, {owner: actor.session_id, type: 'player'})[0];
        if (!player)
        {
            let data =
            {
                id: guid.create (),
                type: 'player',
                owner: actor.session_id,
                position: [-5 + 10 * Math.random (), 0, -5 + 10 * Math.random ()],
                velocity: [0, 0, 0]
            };

            this.createEntity (data);
            this.app.fire ('client:send', data);
        } 
    }

    //----------------------------------------------------------------------------------//
    //                                      Utils                                       //
    //----------------------------------------------------------------------------------//

    createEntity (data)
    {
        switch (data.type)
        {
            case 'player':
                let player = this.templates.player.resource.instantiate ();
                player.name = data.id;
                player.script?.get ('Player')?.setup (data);
                this.entity.addChild (player);
                break;
        }
    }

    destroyEntity (entity)
    {
        entity.destroy ();
    }

    find (snapshot, params)
    {
        let result = [];
        for (let entry of snapshot.values ())
            if (Object.entries (params).every (([key, value]) => entry[key] === value))
                result.push (entry);

        return result;
    }
}
```
{% endtab %}

{% tab title="player.mjs" %}
```javascript
// @ts-nocheck
import { Script, Vec2 } from 'playcanvas';
import { KEY_UP, KEY_DOWN, KEY_LEFT, KEY_RIGHT} from 'playcanvas';
import { KEY_W, KEY_S, KEY_A, KEY_D } from 'playcanvas';


export default class Player extends Script
{
    static scriptName = 'Player';

    initialize () {}

    setup (data)
    {
        this.entity.rigidbody.teleport (...data.position);
        this.entity.rigidbody.linearVelocity = new pc.Vec3 (...data.velocity);
    }

    //----------------------------------------------------------------------------------//
    //                                      Update                                      //
    //----------------------------------------------------------------------------------//

    update (dt)
    {
        let actor = this.app.state.get ('client.actor');
        let snapshot = this.app.state.get ('client.snapshot');
        
        let data = snapshot.get (this.entity.name);
        if (!data)
            return;

        if (data.owner === actor.session_id)
            this.updateLocal (data);
        else
            this.updateRemote (data);
    }

    updateLocal (data)
    {
        let inputdir = this.processInput ();
        let movedir = new pc.Vec3 (inputdir.x, 0, -inputdir.y);
        let mass = this.entity.rigidbody.mass;
        let force = movedir.clone ().mulScalar (20 * mass);
        this.entity.rigidbody.applyForce (force.x, force.y, force.z);

        this.app.fire ('client:send',
        {
            ...data,
            position: this.entity.getPosition ().toArray (),
            velocity: this.entity.rigidbody.linearVelocity.toArray ()
        });
    }

    updateRemote (data)
    {
        this.entity.rigidbody.teleport (...data.position);
        this.entity.rigidbody.linearVelocity = new pc.Vec3 (...data.velocity);
    }

    //----------------------------------------------------------------------------------//
    //                                      Utils                                       //
    //----------------------------------------------------------------------------------//

    processInput ()
    {
        let dir = new Vec2 ();

        if (this.app.keyboard.isPressed (KEY_W) || this.app.keyboard.isPressed (KEY_UP))
            dir.y += 1;
        if (this.app.keyboard.isPressed (KEY_S) || this.app.keyboard.isPressed (KEY_DOWN))
            dir.y -= 1;
        if (this.app.keyboard.isPressed (KEY_A) || this.app.keyboard.isPressed (KEY_LEFT))
            dir.x -= 1;
        if (this.app.keyboard.isPressed (KEY_D) || this.app.keyboard.isPressed (KEY_RIGHT))
            dir.x += 1;

        return dir.normalize ();
    }
}
```
{% endtab %}
{% endtabs %}

\[...Final testing]

## Conclusion

\[...]
