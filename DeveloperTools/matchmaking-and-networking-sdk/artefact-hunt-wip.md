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





