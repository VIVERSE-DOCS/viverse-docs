---
description: About this page
hidden: true
noIndex: true
---

# Artefact Hunt (WIP)

***

## About

Artefact Hunt is competitive multiplayer game inspired by a family of Tag games, Capture the Flag, and variety of similar multiplayer modes like Mario Kart: Shine Thief, Halo: Oddball, Fortnite: The Getaway, and so forth.

Each players assumes the role of a Hunter — whose goal is to grab an Artefact as quick as possible, and bring it to the Portal before other Hunters steal it from him by touch. When Artefact is picked up or stolen - a powerful Blastwave appears, which knocks away all Hunters in proximity, also destroying the current Artefact's Carrier in the process — all enhanced with slow motion / bullet time effect, synchronized across network. The Artefact take and retake continues until some Hunter finally brings to the Portal and thus wins the round. After that the map is reset and the new round begins.

## Project Overview

The project consists of 2 scenes:

* **Main:** loaded by default and contains core functionality like App, Client, Loader, Input, Physics, View, as well as Main Screen UI
* **Content:** contains everything related to multiplayer map and session — like graphics, lighting, collisions, Game and Player representations, Container for instantiating networked entities, and game-specific UI. It's loaded additively when user joins multiplayer game by pressing Start in the Main Screen UI (see Asset Loading for more details)

### Application

* app.mjs: an entry point of our application. It's main purpose is to emit update-specific events (like `input:update`, `game:update`, `physics:update` and so on) —  so other scripts would update their internal states in particular order. Unlike traditional PlayCanvas approach, where each script uses built-in `update` method — our implementation helps to enforce execution order, which is crucial for eliminating all sorts of possible issues down the road
*   state.mjs: a convenient global store object which helps to pass data between scripts in immutable way where it's needed. In a nutshell, one script may write some value under specific key as a result of its update, and another script will read it and use it for updating its internal state, like so:

    ```javascript
    /* Script A */    this.app.state.set ('unique.key', 1);
    /* Script B */    const value = this.app.state.get ('unique.key'); // value = 1
    ```

### Asset Loading

* loader.mjs: loads assets for the Content scene, and then instantiates that scene when the user enters multiplayer game. \[...To understand which assets to when we're using built-in Tags system]

### Input Handling

* input.mjs: responsible for handling raw inputs like keyboard, mouse and touch, and converting them into virtual controls like `movepad` and `pointer`, which are then used for player movement and UI elements

### Networking

* client.mjs: encapsulates all functionality related to networking by implementing Viverse Matchmaking and Multiplayer SDKs. It keeps internal representation of networked game state in a form of Snapshot object, and exposes it to other scripts to read and write to, via `this.app.snapshot` variable
* snapshot.mjs: data structure representing networked game state, which is constantly updated by incoming and outgoing messages. \[...See more detailed overview below

### Physics

* physics.mjs: discontinues default PlayCanvas Physics update and updates simulation in particular order instead — after `game:update` but before `game:postupdate` event. Also, implements slow motion / bullet time effect during Artefact pickup or steal, by diluting update time. \[...See more on that below]
* collider.mjs: provides special collision groups and masks for all colliders in the game. This is mostly necessary due to Local and Remote Players behaving differently, and how local movement prediction for Remote Players is implemented. \[...See more on that below]



## Multiplayer Implementation





