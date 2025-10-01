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

This tutorial assumes you've completed \[Part 02] and already familiar with the basics of [VIVERSE Play SDK](../matchmaking-and-networking-sdk.md) / Networking functionality. Please feel free to revisit those if you need a quick recap!

In the second part, we'll focus on \[...]. You can follow this tutorial by forking a dedicated \[PlayCanvas Project] with all the code and assets included.

## Step 1

\[... Intro about architecture design and state handling]

## Step 2

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

    update (dt)
    {
        // nothing here for now
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
            return super.set (key, value.clone ());
        else if (value instanceof Map)
            return super.set (key, value);
        else if (Array.isArray (value))
            return super.set (key, [...value]);
        else if (value instanceof Object)
            return super.set (key, {...value});
        else
            return super.set (key, value);
    }

    get (key)
    {
        let value = super.get (key);

        if (value === undefined || value === null)
            return value;
        else if (typeof value.clone === 'function')
            return value.clone ();
        else if (value instanceof Map)
            return value;
        else if (Array.isArray (value))
            return [...value];
        else if (value instanceof Object)
            return {...value};
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

## Step 3

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

    update (dt)
    {
        this.app.fire ('client:update', dt);
    }

    postUpdate (dt)
    {
        this.app.state.log ();
    }
}
```
{% endtab %}

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
        this.username = 'A123';
        this.play = new viverse.Play ();

        this.data =
        {
            ready: false,
            actor: null,
            room: null,
            snapshot: new Map ()
        };

        this.app.on ('client:update', this.handleUpdate, this);
    }

    postInitialize ()
    {
        this.app.state.set ('client.ready', this.data.ready);
        this.app.state.set ('client.actor', this.data.actor);
        this.app.state.set ('client.room', this.data.room);
        this.app.state.set ('client.snapshot', this.data.snapshot);

        this.initClient ();
    }

    async initClient ()
    {
        await this.initMatchmaking ();
        await this.createJoinRoom ();
        await this.initMultiplayer ();

        this.multiplayer.general.onMessage (this.handleMessageIn.bind (this));
        this.app.on ('client:send', this.handleMessageOut, this);

        this.data.ready = true;
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

    handleUpdate (dt)
    {
        this.data.actor = this.matchmaking?.currentActor;
        this.data.room = this.matchmaking?.currentRoom;

        let ids = this.data.room?.actors.map (actor => actor.session_id) || [];
        for (let [key, data] of this.data.snapshot.entries ())
            if (!ids.includes (data.owner))
                this.data.snapshot.delete (key);

        this.app.state.set ('client.ready', this.data.ready);
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
}
```
{% endtab %}
{% endtabs %}

\[...Showing Client states in session storage]

## Step 4

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

    update (dt)
    {
        this.app.fire ('client:update', dt);
        this.app.fire ('game:update', dt);
    }

    postUpdate (dt)
    {
        this.app.state.log ();
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

    initialize ()
    {
        this.app.on ('game:update', this.handleUpdate, this);
    }

    //----------------------------------------------------------------------------------//
    //                                      Update                                      //
    //----------------------------------------------------------------------------------//

    handleUpdate (dt)
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

## Step 5

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

    update (dt)
    {
        this.app.fire ('client:update', dt);
        this.app.fire ('game:update', dt);
    }

    postUpdate (dt)
    {
        this.app.state.log ();
    }
}
```
{% endtab %}

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

    initialize ()
    {
        this.app.on ('game:update', this.handleUpdate, this);
    }

    //----------------------------------------------------------------------------------//
    //                                      Update                                      //
    //----------------------------------------------------------------------------------//

    handleUpdate (dt)
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

    initialize ()
    {
        this.app.on ('game:update', this.handleUpdate, this);
        this.once ('destroy', this.destroy, this);
    }

    setup (data)
    {
        this.entity.rigidbody.teleport (...data.position);
        this.entity.rigidbody.linearVelocity = new pc.Vec3 (...data.velocity);
    }

    //----------------------------------------------------------------------------------//
    //                                      Update                                      //
    //----------------------------------------------------------------------------------//

    handleUpdate (dt)
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

    //----------------------------------------------------------------------------------//
    //                                    Destructor                                    //
    //----------------------------------------------------------------------------------//

    destroy ()
    {
        this.app.off ('game:update', this.handleUpdate, this);
    }
}
```
{% endtab %}
{% endtabs %}

\[...Final testing]

## Conclusion

\[...]
