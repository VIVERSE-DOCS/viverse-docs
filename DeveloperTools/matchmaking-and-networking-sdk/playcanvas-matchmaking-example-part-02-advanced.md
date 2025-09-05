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

And while that is a decent introduction to Matchmaking SDK functionality — it doesn't do great job at actually mapping that functionality to the real world application. How do we know when user enters the Room after leaving the Lobby, and vice versa? What if we want to show special Loading screen every time a new SDK request is being made? What if we want some code to execute only once each time user enters the Room?

In order to prepare our application for all this extra complexity we might want to refactor current code into something more robust and resilient. \[And that's where State Flow might be an interesting topic to discuss.]

#### Heading

First, let's introduce a concept of Application State - a single point in discreet space of all possible configurations that can meaningfully describe our application in any given moment. Application can only be in one State at a time, and its current State is \[inert on its own] — it switches to another State only when certain conditions are met. These conditions can be \[...]

Let's define 6 distinct States our application can take, along with \[...functionality]:

* `Init State` : initialize Matchmaking client, setup user's Actor, go to Lobby State
* `Lobby State` : show available Rooms, handle user request to Create or Join the Room
* &#x20;`Create State` : ask SDK to create the Room, handle response, go to Room State
* &#x20;`Join State` : ask SDK to join the Room, handle response, go to Room State
* &#x20; `Room State` : show Actors currently in the Room,  handle user request to Leave the Room
* &#x20;`Leave State` : ask SDK to leave the Room, handle response, back to Lobby State

Here is a State Flow graph:

<figure><img src="../.gitbook/assets/stateflow.jpg" alt=""><figcaption></figcaption></figure>



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

        // NOTE: Matchmaking Client has only .on() method to subscribe to events
        // We implement our own .off() to make our code look sleeker later on
        
        this.matchClient = await this.playClient.newMatchmakingClient (this.appId);
        this.matchClient.off = (event) => this.matchClient.eventListeners.delete (event);
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

        // Subscribing to Room List updates when entering the Lobby
        this.matchClient.on ('onRoomListUpdate', ((rooms) =>
            console.log (`::: Existing Rooms:`, rooms.map (room => room.id))));
        
        // Waiting for creating or joining the Room via console
        // >.. await create ()
        // >.. await join ('...')
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

#### Testing

Alright, time to give a test run to this new refactored architecture! As previously, let's launch our PlayCanvas app in two or more separate tabs and use globally exposed `create ()`, `join ()` and `leave ()` methods to trigger corresponding States. If you did everything correctly you would see something like this:

<div><figure><img src="../.gitbook/assets/mm4a.png" alt="" width="375"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/mm4b.png" alt="" width="375"><figcaption></figcaption></figure></div>



### Chapter 2: Integrating PlayCanvas UI system

TODO

<figure><img src="../.gitbook/assets/mm5 (1).png" alt=""><figcaption></figcaption></figure>

```javascript
import { Script, Entity, guid } from 'playcanvas';
const { viverse } = globalThis;

/** @interface */
class Screens
{
    /** @title Loading @type {Entity} */ loading;
    /** @title Lobby @type {Entity} */  lobby;
    /** @title Room @type {Entity} */   room;
}

export class Main extends Script
{
    static scriptName = 'Main';

    /** @attribute @title Screens @type {Screens} */  screens;

    initialize () {...}
    
    //----------------------------------------------------------------------------//
    //                                 State Flow                                 //
    //----------------------------------------------------------------------------//
    
    async gotoInitState ()
    {
        this.showScreen ('loading');
        // ... rest of the code
        // [Initialization complete] -> Lobby State
    };

    async gotoLobbyState ()
    {
        this.showScreen ('lobby');
        // ... rest of the code
        // [On user action] -> Create State | Join State
    };

    async gotoCreateState ()
    {
        this.showScreen ('loading');
        // ... rest of the code
        // [Room creation complete] -> Room State
    };

    async gotoJoinState (id)
    {
        this.showScreen ('loading');
        // ... rest of the code
        // [Room joining complete] -> Room State
    };

    async gotoRoomState ()
    {
        this.showScreen ('room');
        // ... rest of the code
        // [On user action] -> Leave State
    };

    async gotoLeaveState ()
    {
        this.showScreen ('loading');
        // ... rest of the code
        // [Room leaving complete] -> Lobby State
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
    
    showScreen (id)
    {
        this.screens.loading.enabled = (id === 'loading');
        this.screens.lobby.enabled = (id === 'lobby');
        this.screens.room.enabled = (id === 'room');
    }
}
```

### Chapter 3: Populating UI elements and wiring buttons to user actions

TODO

<figure><img src="../.gitbook/assets/mm6.png" alt=""><figcaption></figcaption></figure>

```javascript
// @ts-nocheck
import { Script, Entity, guid } from 'playcanvas';
const { viverse } = globalThis;

/** @interface */
class Screens
{
    /** @title Loading @type {Entity} */ loading;
    /** @title Lobby @type {Entity} */  lobby;
    /** @title Room @type {Entity} */   room;
}

/** @interface */
class Buttons
{
    /** @title Create @type {Entity} */ create;
    /** @title Leave @type {Entity} */  leave;
    /** @title Join @type {Entity[]} */ join;
}

/** @interface */
class Elements
{
    /** @title User Name @type {Entity} */  username;
    /** @title Room Name @type {Entity} */  roomname;
    /** @title Player Count @type {Entity} */ counter;
}

export class Main extends Script
{
    static scriptName = 'Main';

    /** @attribute @title Screens @type {Screens} */  screens;
    /** @attribute @title Buttons @type {Buttons} */  buttons;
    /** @attribute @title Elements @type {Elements} */  elements;

    initialize () {...}

    //----------------------------------------------------------------------------//
    //                                 State Flow                                 //
    //----------------------------------------------------------------------------//
    
    async gotoInitState ()
    {
        this.showScreen ('loading');

        // ... rest of the code
        // [Initialization complete] -> Lobby State
    }

    async gotoLobbyState ()
    {
        this.showScreen ('lobby');
        
        // Display current username in Lobby screen
        this.elements.username.element.text = `Guest ${this.username}`;
        
        // Wire Create button to switch app state to Create
        this.buttons.create.element.off ('click');
        this.buttons.create.element.on ('click', async () =>
        {
            await this.gotoCreateState ();
        });

        // Update Join buttons each time the Room List is updated
        // Wire each Join button to Join state with respective room id
        this.hideJoinButtons ();
        this.matchClient.on ('onRoomListUpdate', (rooms) =>
        {
            this.showJoinButtons (rooms, async (room) =>
            {
                await this.gotoJoinState (room.id);
            });
        });
    }

    async gotoCreateState ()
    {
        this.showScreen ('loading');
        
        // ... rest of the code
        // [Room creation complete] -> Room State
    }

    async gotoJoinState (id)
    {
        this.showScreen ('loading');
        
        // ... rest of the code
        // [Room joining complete] -> Room State
    }

    async gotoRoomState ()
    {
        this.showScreen ('room');

        // Display current room name in Room Screen
        this.elements.roomname.element.text = this.matchClient.currentRoom.name;
        
        // Update player counter each time the Actor List is changed
        this.matchClient.on ('onRoomActorChange', (actors) =>
        {
            this.elements.counter.element.text = `${actors.length}  PLAYER`;
            this.elements.counter.element.text += actors.length > 1 ? 'S' : '';
        });

        // Wire Leave button to switch app state to Leave
        this.buttons.leave.element.off ('click');
        this.buttons.leave.element.on ('click', async () =>
        {
            await this.gotoLeaveState ();
        });
    }
    
    async gotoLeaveState ()
    {
        this.showScreen ('loading');

        // ... rest of the code
        // [Room leaving complete] -> Lobby State
    }

    //----------------------------------------------------------------------------//
    //                                   Utils                                    //
    //----------------------------------------------------------------------------//
    
    randomUsername () {...}

    showScreen (id) {...}

    hideJoinButtons ()
    {
        for (let button of this.buttons.join)
            button.enabled = false;
    }

    showJoinButtons (rooms, callback)
    {
        this.hideJoinButtons ();

        for (let i = 0; i < rooms.length; i++)
        {
            let room = rooms[i];
            let button = this.buttons.join[i];
            
            // For simplicity our Lobby screen has only 4 Join Buttons instantiated
            // So only first 4 Rooms will be parsed and available for joining
            // The 5th and subsequent Rooms will be ignored
            
            if (button)
            {
                button.enabled = true;
                button.findByName ('Room Name').element.text = room.name;
                button.findByName ('Counter').element.text = room.actors.length;

                button.element.off ('click');
                button.element.on ('click', async () => await callback (room));
            }
        }
    }
}

```
