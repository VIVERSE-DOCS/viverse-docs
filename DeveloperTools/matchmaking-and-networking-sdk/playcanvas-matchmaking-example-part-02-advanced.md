---
description: >-
  Learn how to integrate VIVERSE Play SDK Matchmaking into your standalone
  PlayCanvas project using async state flow and PlayCanvas UI system
hidden: true
noIndex: true
---

# PlayCanvas Matchmaking example: Part 02 - Advanced

### Pre-requisite: \[Part 01 tutorial]

TODO

### Chapter 1: App architecture and introduction to Async State Flow

#### Introduction

In [Part 01](playcanvas-matchmaking-example-part-01-basics.md#step-5-create-join-and-leave-the-room-and-receive-relevant-updates) of this tutorial we ended up devising 4 essential methods to work with our Matchmaking Client — `initMatchClient`, `createRoom`, `joinRoom` and `leaveRoom`, along with 2 useful event listeners —  `onRoomListUpdate` and `onRoomActorChange` — to receive live updates when a new Room is created or when current room's Actor List is updated.

And while that is a decent introduction to Matchmaking SDK functionality — it doesn't do great job at actually mapping that functionality to the real world application. How do I know when user enters the Room after leaving the Lobby, and vice versa? What if I want to show special Loading screen while SDK is initializing? What if I want some code to execute exactly once each time user enters the Room?

In order to prepare our application for all this extra complexity we might want to refactor current code into something more robust and resilient. \[And that's where State Flow might be an interesting topic to discuss.]

#### Heading

First, let's introduce a concept of Application State - a single point in discreet space of all possible states that can meaningfully describe our application.

\[State -> State via action and ...]

<figure><img src="../.gitbook/assets/stateflow.jpg" alt=""><figcaption></figcaption></figure>

* `Init State` : initialize Matchmaking client, setup user's Actor
* `Lobby State` : show available Rooms, handle user request to Create or Join the Room
* &#x20;`Create State` : ask SDK to create the Room, handle response
* &#x20;`Join State` : ask SDK to join the Room, handle response
* &#x20; `Room State` : show Actors currently in the Room,  handle user request to Leave the Room
* &#x20;`Leave State` : ask SDK to leave the Room, handle response

```javascript
// @ts-nocheck
import { Script, guid } from 'playcanvas';
const { viverse } = globalThis;

export class Main extends Script
{
    static scriptName = 'Main';

    initialize ()
    {
        this.appId = 'ajhzug2zwb'; // replace with your App ID
        this.username = 'A123';
        this.playClient = new viverse.Play ();

        // We're exposing these 3 actions to global window object 
        // So we can create / join / leave the Room via browser console
        // Without setting up PlayCanvas UI at this point
        
        window.create = this.gotoCreateState.bind (this);
        window.join = this.gotoJoinState.bind (this);
        window.leave = this.gotoLeaveState.bind (this);
        
        this.gotoInitState ();
    }

    //----------------------------------------------------------------------------//
    //                                 State Flow                                 //
    //----------------------------------------------------------------------------//
    //          INIT -> LOBBY -> CREATE / JOIN -> ROOM -> LEAVE -> LOBBY          //
    //----------------------------------------------------------------------------//

    async gotoInitState ()
    {
        console.log ('>>> Username:', this.username);
        console.log ('>>> Init matchmaking...');

        this.matchClient = await this.playClient.newMatchmakingClient (this.appId);
        this.matchClient.on ('onConnect', async () =>
        {
            await this.matchClient.setActor
            ({
                name: this.username,
                session_id: guid.create (), // unique random string
                properties: {}
            });

            await this.gotoLobbyState ();
        });
    };

    async gotoLobbyState ()
    {
        console.log ('>>> In the Lobby');
        
        // Waiting for creating or joining the Room via console
        // >.. await create () OR await join ('...')
    };

    async gotoCreateState ()
    {
        console.log ('>>> Creating room...');

        let {success} = await this.matchClient.createRoom
        ({
            name: `${this.username}'s Room`, mode: 'pvp',
            minPlayers: 1, maxPlayers: 4, properties: {}
        });

        if (success) await this.gotoRoomState ();
        else await this.gotoLobbyState ();
    };

    async gotoJoinState (id)
    {
        console.log ('>>> Joining room...');

        let {success} = await this.matchClient.joinRoom (id);

        if (success) await this.gotoRoomState ();
        else await this.gotoLobbyState ();
    };

    async gotoRoomState ()
    {
        console.log ('>>> In the Room:', this.matchClient.currentRoom.id);
        
        // Waiting for leaving the Room via console
        // >.. await leave ()
    };

    async gotoLeaveState ()
    {
        console.log ('>>> Leaving room...');

        await this.matchClient.leaveRoom ();
        await this.gotoLobbyState ();
    };
}
```

### Chapter 2: \[Further improvements and live updates]

TODO

```javascript
// @ts-nocheck
import { Script, guid } from 'playcanvas';
const { viverse } = globalThis;

export class Main extends Script
{
    static scriptName = 'Main';

    initialize ()
    {
        this.appId = 'ajhzug2zwb'; // replace with your App ID
        this.username = this.randomUsername ();
        this.playClient = new viverse.Play ();
        
        // We're exposing these 3 actions to global window object 
        // So we can create / join / leave the Room via browser console
        // Without setting up PlayCanvas UI at this point
        
        window.create = this.gotoCreateState.bind (this);
        window.join = this.gotoJoinState.bind (this);
        window.leave = this.gotoLeaveState.bind (this);
        
        this.gotoInitState ();
    }

    //----------------------------------------------------------------------------//
    //                                 State Flow                                 //
    //----------------------------------------------------------------------------//
    //          INIT -> LOBBY -> CREATE / JOIN -> ROOM -> LEAVE -> LOBBY          //
    //----------------------------------------------------------------------------//

    async gotoInitState ()
    {
        console.log ('>>> Username:', this.username);
        console.log ('>>> Init matchmaking...');

        this.matchClient = await this.playClient.newMatchmakingClient (this.appId);
        this.matchClient.on ('onConnect', async () =>
        {
            await this.matchClient.setActor
            ({
                name: this.username,
                session_id: guid.create (), // unique random string
                properties: {}
            });

            await this.gotoLobbyState ();
        });

        // IMPORTANT!
        // Matchmaking Client has .on() method to subscribe to events
        // But as of v1.3 it doesn't have .off() method to unsubscribe from them
        // So we implement our own .off() to make our code look better later on
        this.matchClient.off = (event) => this.matchClient.eventListeners.delete (event);
    };

    async gotoLobbyState ()
    {
        console.log ('>>> In the Lobby');

        // Subscribing to Room List updates when entering the Lobby
        this.matchClient.on ('onRoomListUpdate', ((rooms) =>
            console.log (`::: Existing Rooms:`, rooms.map (room => room.id))));
        
        // Waiting for creating or joining the Room via console
        // >.. await create () OR await join ('...')
    };

    async gotoCreateState ()
    {
        console.log ('>>> Creating room...');

        // Unsubscribing from Room List updates when leaving the Lobby
        this.matchClient.off ('onRoomListUpdate');

        let {success} = await this.matchClient.createRoom
        ({
            name: `${this.username}'s Room`, mode: 'pvp',
            minPlayers: 1, maxPlayers: 4, properties: {}
        });

        if (success) await this.gotoRoomState ();
        else await this.gotoLobbyState ();
    };

    async gotoJoinState (id)
    {
        console.log ('>>> Joining room...');

        // Unsubscribing from Room List updates when leaving the Lobby
        this.matchClient.off ('onRoomListUpdate');

        let {success} = await this.matchClient.joinRoom (id);

        if (success) await this.gotoRoomState ();
        else await this.gotoLobbyState ();
    };

    async gotoRoomState ()
    {
        console.log ('>>> In the Room:', this.matchClient.currentRoom.id);

        // Subscribing to Actor List updates when entering the Room
        this.matchClient.on ('onRoomActorChange', ((actors) =>
            console.log (`::: Actors in the Room:`, actors.map (actor => actor.name))));
        
        // Waiting for leaving the Room via console
        // >.. await leave ()
    };

    async gotoLeaveState ()
    {
        console.log ('>>> Leaving room...');

        // Unsubscribing from Actor List updates when leaving the Room
        this.matchClient.off ('onRoomActorChange');

        await this.matchClient.leaveRoom ();
        await this.gotoLobbyState ();
    };

    //----------------------------------------------------------------------------//
    //                                   Utils                                    //
    //----------------------------------------------------------------------------//

    randomUsername ()
    {
        let username = '';
        username += 'ABCDEFGHIJKLMNOPQRSTUVWXYZ' [Math.floor (Math.random () * 26)];
        for (let i = 0; i < 3; i++)
            username += '0123456789' [Math.floor (Math.random () * 10)];
        return username;
    }
}
```

### Chapter 3: \[Integrating PlayCanvas UI system]

TODO
