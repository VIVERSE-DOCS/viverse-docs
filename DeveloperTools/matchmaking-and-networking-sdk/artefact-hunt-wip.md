---
description: About this page
hidden: true
noIndex: true
---

# Artefact Hunt (WIP)

***

## About

Artefact Hunt is competitive multiplayer game inspired by a family of Tag games, Capture the Flag, and variety of similar game modes — like Mario Kart: Shine Thief, Halo: Oddball, Fortnite: The Getaway, and so forth.

Each players assumes the role of a Hunter — whose goal is to grab an Artefact as quick as possible, and bring it to the Portal before other Hunters steal it from him by collision. When Artefact is picked up on the map or stolen from another Hunter, a powerful Blastwave appears, which knocks away all Hunters in proximity, also destroying the current Artefact's carrier in the process — all enhanced with slow motion / bullet time effect, synchronized across network. The Artefact take and retake continues until some Hunter finally brings it to the Portal and thus wins the round. After that the map is reset and the the new round begins.

## Prerequisites

\[...]

## Project Overview

The project consists of 2 scenes:

* **Main:** loaded by default and contains core functionality like App, Client, Loader, Input, Physics, View, as well as Main Screen UI
* **Content:** contains everything related to multiplayer map and session — like graphics, lighting, colliders, Game and Player representations, Container for instantiating networked entities, and game-specific UI. It's loaded additively when user joins multiplayer game by pressing Start in the Main Screen UI (see Asset Loading for more details)

### Application

`app.mjs` `state.mjs`

* We start with the **App** script which is the entry point of our application. Its main purpose is to emit update-specific events (like `input:update`, `game:update`, `physics:update` and so on) —  so other scripts would update their internal states in particular order. Unlike traditional PlayCanvas approach, where each script uses built-in `update` method — our implementation helps to enforce execution order, which is crucial for eliminating all sorts of possible issues down the road.
*   During its initialization, the App instantiates **State** object — which is a convenient global store map that helps to pass data between scripts in immutable way where it's needed. In a nutshell, one script may write some value under specific key as a result of its update, and another script will read that value and use it for updating its internal state, like so:<br>

    ```javascript
    /* Script A */  this.app.state.set ('unique.key', 1);
    /* Script B */  const value = this.app.state.get ('unique.key'); // value = 1;
    ```

### Asset Loading

`loader.mjs`

* **Loader** script performs asset loading for the Content scene, and then instantiates that scene when the user enters multiplayer game. All assets used in Content scene have their `preload` attribute turned off, and also have `game` tag applied — so they're ignored during initial PlayCanvas preloading and then loaded on demand when needed

### User Input

`input.mjs`

* **Input** is responsible for handling raw inputs like keyboard, mouse and touch, and converting them into virtual controls like `movepad` and `pointer`, which are then used for player movement and UI elements. For mobile devices, we're using internal heuristics to discern between touch moves and taps to allocate them to `movepad` and `pointer` respectively

### Networking

`client.mjs` `snapshot.mjs`

* **Client** encapsulates all functionality related to networking by implementing Viverse Matchmaking and Multiplayer SDKs. It keeps internal representation of networked game state in a form of Snapshot object, and exposes it to other scripts to read and write to, via globally accessible `this.app.snapshot` variable
* **Snapshot** is a data structure representing networked game state, which is constantly updated by incoming and outgoing messages. For more details, please see **Multiplayer Implementation** below

### Entities

`game.mjs` `player.mjs` `container.mjs`\
`hunter.mjs` `artefact.mjs` `portal.mjs` `blastwave.mjs` `etc`

* **Game** is a static Entity responsible for administrative part of the experience — i.e. managing meta state, when and where the Artefact or Portal are spawned, updating Blastwave internal timer (that affects Physics time dilation), and a few other things. Even though Game entity exists for all clients locally, only Master client is responsible for updating it — other clients just receive those updates
* **Player** is another static Entity which represents the user and its associated data - i.e. the username and the current score. It's responsible for respawning a Hunter when user clicks a button, and incrementing user score if associated Hunter brings an Artefact to a Portal. Each client updates its own Player entity locally, and those updates are broadcasted to other clients via `snapshot.update(...)` call. So if everything runs correctly, the amount of `player` entries in the Snapshot should correspond to amount of users currently connected to the Room
* **Container** acts as a manager for dynamic networked Entities, trying to sync their presence with the Snapshot data. With each update, it goes through the list of current Snapshot entries, and compares it with the list of its children (i.e. already instantiated Entities):
  * If new Snapshot entry exists, but no corresponding Entity can be found — that Entity is created and linked to that entry
  * If Entity is still present, but its corresponding Snapshot entry no longer exists (i.e. was deleted) — that Entity is destroyed
* **Hunter** is dynamic Entity encapsulating a physical embodiment of player on the map. Hunter can be spawned or destroyed multiple times during the game, it has position and velocity which are updated locally from the Input, and also health which is reduced to zero in the event of an Artefact steal. Hunter's implementation is the most extensive in this project, so for convenience it's divided into multiple parts:
  * `hunter.mjs` : main script responsible for snapshot handling and updating submodules
  * `hunter.move.mjs` : a submodule containing everything related to movement, both local and remote, with physics and client-side prediction
  * `hunter.int.mjs` : responsible for handling interactions with the Artefact, Portal, and other Hunters, and sending corresponding actions to Viverse servers for competition resolution
  * `hunter.visual.mjs` : handles various visual aspects, like blob shadow, carrier's red glow, and so on
* **Artefact** is another important dynamic Entity in our game. Similar to the Hunter, it can be spawned and destroyed, has position on the map, but also a carrier attribute that refers to id of a Hunter that is currently carrying it. And similar to the Game, it's replicated for all clients locally, but only Master client is responsible for creating and updating it
* **Portal** is one more dynamic Entity, but a much simpler one. It has only position, and mostly provides just a trigger area for current Carrier to collide with, in order to win the round. And similar to the Artefact, only Master client can spawn it
* **Blastwave** is the last dynamic Entity controllable by Master client. It's created during Artefact pickup or steal, and exposes dynamically changing slowmo attribute that affects local Physics timescale for all clients in the game. See Physics section below for more details

### Physics

`physics.mjs` `collider.mjs`&#x20;

* **Physics** script discontinues default PlayCanvas physics update and updates simulation in particular order instead — after `game:update` but before `game:postupdate` event. But its main side feature is to implement slow motion / bullet time effect during Artefact pickup or steal, by dynamically dilating update time
* **Collider** provides special collision groups and masks for all colliders in the game. This is mostly necessary due to Local and Remote Hunters behaving differently, and how local movement prediction for Remote Hunters is implemented. See Hunter section above for more details

## Multiplayer Implementation

### Snapshot

Snapshot is essential concept lying at the heart of our networking implementation. It's a key-value map consisting of multiple Entries — plain JS objects with `id`, `type` and additional attributes:

```javascript
{id: 'qwe-123', type: 'game', mode: 'warmup', timer: 2.5}
{id: 'asd-456', type: 'player', owner: '...', username: 'Blue Banana', score: 3}
{id: 'zxc-789', type: 'hunter', owner: '...', position: [...], velocity: [...], ...}
{id: 'rty-234', type: 'artefact', position: [...], carrier: '...'}
```

It's a minimum viable representation of our current game state — what entities currently exist in the world, what are their internal states, attribute values, and so on. Because our networking is essentially P2P, there is no single source of truth for that game state — each Client keeps its own local Snapshot, trying to meaningfully synchronize it with the Snapshots of other Clients.

### Entity

Where Snapshot entry is just a plain JS object, the Entity is a physical manifestation of that object in the game world. Each Entity has a corresponding script attached to it, which is responsible for processing the Snapshot entry associated with this Entity.

All Entities could be divided into 2 types and 2 categories respectively:

<table><thead><tr><th width="105.01171875">Entities</th><th width="90.6484375">Owner</th><th valign="middle">Local</th><th>Remote</th></tr></thead><tbody><tr><td><code>player</code><br><code>hunter</code></td><td>Client</td><td valign="middle"><sub>Update internal state of an Entity and broadcast modified Snapshot entry to other clients</sub></td><td><sub>Receive updated Snapshot entry, store it locally, and update Entity's internal state based on it</sub></td></tr><tr><td><code>game</code><br><code>artefact</code><br><code>portal</code><br><code>blastwave</code></td><td>Master</td><td valign="middle"><sub>Update internal state of an Entity and broadcast modified Snapshot entry to other clients</sub></td><td><sub>Receive updated Snapshot entry, store it locally, and update Entity's internal state based on it</sub></td></tr></tbody></table>

Types by entity's owner:

* Client: these entities are handled by their respective owning Clients, and they have a distinctive `owner` attribute in their Snapshot entries. In practise it means that only associated Client can modify that Snapshot entry locally, and then broadcast those updates to all other clients for synchronization
* Master: these entities can be handled only by the Master Client, so in that sense they're implicitly owned by it. Other than that, it's the same principle — owner (Master Client) updates associated Snapshot entries, and these updates are propagated to all other clients across network

```javascript
Clients:
>>> A: {session_id: 'abc', is_master_client: true}
>>> B: {session_id: 'xyz', is_master_client: false}

Snapshot entries:
{id: 'qwe-123', type: 'game', mode: 'warmup', timer: 15}
{id: 'asd-456', type: 'player', owner: 'abc', username: 'Player 01', score: 2}
{id: 'zxc-789', type: 'player', owner: 'xyz', username: 'Player 02', score: 3}

// Client A updates GAME because it's a master client
// Client A also updates PLAYER 01 because it's the owner of it
// Client B only updates PLAYER 02
```

Categories by network replication:

* Local:&#x20;
* Remote:

```javascript
Clients:
>>> A: {session_id: 'abc'}
>>> B: {session_id: 'xyz'}

Snapshot entries:
{id: 'h1', type: 'hunter', owner: 'abc', position: [...], velocity: [...], ...}
{id: 'h2', type: 'hunter', owner: 'xyz', position: [...], velocity: [...], ...}

// For Client A the 1st Hunter is Local, but the 2nd one is Remote
// Client A updates Hunter's position / velocity from the input data
// And broadcasts the result of that update to other clients
```



























