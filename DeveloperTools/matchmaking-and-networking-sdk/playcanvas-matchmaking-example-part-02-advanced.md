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

### Chapter 1: \[Async state flow, refactoring]

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

### Chapter 2: \[Integrating PlayCanvas UI system]

TODO
