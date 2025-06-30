---
description: >-
  This guide introduces the Play SDK and explains how to integrate core
  multiplayer features into VIVERSE  Studio content. It covers setup and usage
  for features such as matchmaking, session management,
---

# Matchmaking & Networking SDK

***

> **BEFORE GETTING STARTED:** you must [authenticate with VIVERSE](login-and-authentication-for-the-sdk.md), including App ID creation in VIVERSE Studio, before requesting Play SDK services.

## Initialize the \`playClient\` instance

Before using any Play SDK features, you must initialize the client instance. This global reference ensures that the Play SDK is available throughout your application.

```
globalThis.playClient = new globalThis.viverse.play();
```

## Matchmaking API

The matchmaking and networking APIs must then be initialized individually.

```
globalThis.matchmakingClient = await playClient.newMatchmakingClient(appId, debug);
```

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

> **NOTE:** Support for random and ticket-based matchmaking is under development.

### Setup Actor Info

Set the player’s session ID, name, and custom properties in the current room.

This API should be called before creating or joining a room. The SDK will store the actor information and automatically attach it to&#x20;the player upon entering the room.

```
matchmakingClient.setActor({
    session_id: session_id,
    name: name,
    properties: {
        "level": 8,
        "skill": 8
    }
});

// Possible return values:
// {
//    "success": true
// }
//     or
// {
//     "success": false,
//     "message": "error message"
// }
```

### Create & Configure a Room

Call `createRoom()` with a configuration object like so:

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Example code:

<pre><code>matchmakingClient.createRoom({
    name: 'Casey's room',
    mode: 'team',
    maxPlayers: 4,
    minPlayers: 2,
    properties: {
        "level": 13,
<strong>        "skill": 21
</strong>    }
});

// Return values:
//
// {
//    "success": true,
//    "room": {
//        "id": "14aa0657-3fd9-438a-a4b0-e6af349d8600",
<strong>//        "app_id": "app_id",
</strong><strong>//        "mode": "team",
</strong>//        "name": "Casey's room",
//        "actors": [
//            {
//                "session_id": "ac98d013-a9c5-4a5f-ab47-727d2951bd4a",
//                "name": "Casey",
//                "properties": {
<strong>//                    "level": 13,
</strong>//                    "skill": 21
<strong>//                },
</strong>//                "is_master_client": true
<strong>//            }
</strong>//         ],
//         "max_players": 4,
//         "min_players": 2,
//         "is_closed": false,
//         "properties": {},
//         "master_client_id": "ac98d013-a9c5-4a5f-ab47-727d2951bd4a",
//         "game_session": "14aa0657-3fd9-438a-a4b0-e6af349d8600",
//         "created_by_me": true
//     }
<strong>// }
</strong><strong>//     or
</strong>// {
//     "success": false,
//     "message": "error message"
// }

</code></pre>

### Join Room by RoomID

Join an existing room by its room ID.

<figure><img src=".gitbook/assets/Screenshot 2025-06-10 at 3.40.05 PM.png" alt=""><figcaption></figcaption></figure>

Example code:

```
await matchmakingClient.joinRoom(room_id);

//Return Value
{
  "success": true,
  "match_id": "2b05c7d2-f47b-4597-ba40-80ea3cdd25df",
  "room": {
    "id": "2b05c7d2-f47b-4597-ba40-80ea3cdd25df",
    "app_id": "app_id",
    "mode": "team",
    "name": "Alex's room",
    "actors": [
      {
        "session_id": "7ca593f1-5880-4b5f-a420-d556a74d3b2d",
        "name": "Alex",
        "properties": {
          "level": 8,
          "skill": 8
        },
        "is_master_client": true
      },
      {
        "session_id": "7f2db5de-f123-49fb-85dd-412e596a9765",
        "name": "Morgan",
        "properties": {
          "level": 18,
          "skill": 9
        },
        "is_master_client": false
      }
    ],
    "max_players": 4,
    "min_players": 2,
    "is_closed": false,
    "properties": {},
    "master_client_id": "7ca593f1-5880-4b5f-a420-d556a74d3b2d",
    "game_session": "2b05c7d2-f47b-4597-ba40-80ea3cdd25df",
    "created_by_me": true
  }
}
Failed Value:
{
  "success": false,
  "message": "error message"
}
```

### Leave Room

Leave the current room that the player has joined.

Example Code:

```
await matchmakingClient.leaveRoom();

//Return Value
{
  "success": true
}

Failed Value:
{
  "success": false,
  "message": "error message"
}
```

### Close Room

Close the current room. Only the room creator can perform this action.

```
await matchmakingClient.closeRoom();

//Return Value
{
  "success": true
}

Failed Value:
{
  "success": false,
  "message": "error message"
}
```

### Get Available Rooms

Retrieve the current list ofavailable rooms that can be joined.

```
matchmakingClient.getAvailableRooms();

//Return Value
{
  "success": true,
  "rooms": [
    {
      "id": "78e4c44a-4d3f-433c-820f-805c0b915dfb",
      "app_id": "app_id",
      "mode": "team",
      "name": "Alex's room",
      "actors": [
        {
          "session_id": "7dc0bf85-ff81-4bcd-907d-1ad35d4aad1f",
          "name": "Alex",
          "properties": {
            "level": 24,
            "skill": 90
          },
          "is_master_client": true
        }
      ],
      "max_players": 4,
      "min_players": 2,
      "is_closed": false,
      "properties": {},
      "master_client_id": "7dc0bf85-ff81-4bcd-907d-1ad35d4aad1f",
      "game_session": "78e4c44a-4d3f-433c-820f-805c0b915dfb"
    }
  ]
}

Failed Value:
{
  "success": false,
  "message": "error message"
}
```

### Get My Room Actors

Get the latest list of all actors (players) currently in the room.

```
matchmakingClient.getMyRoomActors();

//Return Value
{
  "success": true,
  "actors": [
    {
      "session_id": "76192ade-5bb5-4937-835d-c936b7e9e6df",
      "name": "Alex",
      "properties": {
        "level": 22,
        "skill": 33
      },
      "is_master_client": true
    }
  ]
}

Failed Value:
{
  "success": false,
  "message": "error message"
}
```

## Matchmaking Event Listeners

You can listen to matchmaking events using `matchmakingClient.on(eventName, callback)` . These events help you track lobby status, room lifecycle, and player activity in real time.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.33.28 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### onConnect Event

Triggered when the client successfully connects to the SDK.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.42.12 PM.png" alt="" width="348"><figcaption></figcaption></figure>

### onJoinedLobby Event

Triggered when client joins the lobby.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.42.57 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### onJoinRoom Event

Triggered when the client joins a room.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.43.49 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### onRoomListUpdate Event

Triggered when the list of rooms in the lobby is updated.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.48.54 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### onRoomActorChange Event

Triggered when the list of actors in the current room changes.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.49.33 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### onRoomClosed Event

Triggered whe nthe room is successfully closed by the client.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.50.12 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### onError Event

Triggered when an error occurs with a client request.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.50.44 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### stateChange Event

Triggered when the client connection state changes.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.51.14 PM.png" alt="" width="375"><figcaption></figcaption></figure>

## Multiplayer APIs

### Prerequisites

Before using any Multiplayer APIs, you must:

* Initialize the [Play client instance](matchmaking-and-networking-sdk.md#initialize-the-playclient-instance)
* Construct a `MultiplayerClient` instance with a valid room ID and app ID
* Call `init()` to establish the multiplayer session

### Initialize Multiplayer Client

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.53.10 PM.png" alt=""><figcaption></figcaption></figure>

```
//initialize multiplayer client instance
const roomId = 'example_room'; //obtained from matchmaking
const appId = 'example_game'; //App ID from VIVERSE Studio

globalThis.multiplayerClient = new globalThis.play.MultiplayerClient(roomId, appId);
const info = await multiplayerClient.init();
```

{% hint style="info" %}
`MultiplayerClient` is a global class under `play` namespace and is not created via `playClient`. However, Play SDK must still be initialized first.
{% endhint %}

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.56.25 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### Connection Control

Handles the multiplayer client's connection lifecycle. Use these APIs to detect when the client is ready and to manually disconnect when needed.

#### Connect Event

Triggered when the multiplayer client establishes a connection. Use this to perform setup logic after the session is ready.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.57.47 PM.png" alt="" width="375"><figcaption></figcaption></figure>

#### Disconnect

Manually disconnects the multiplayer client instance. Useful for cleanup or leaving the session before navigating away.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 1.59.17 PM.png" alt="" width="338"><figcaption></figcaption></figure>

### General

The `General` module lets creators send and receive custom messages between peers in multiplayer scenes. Use this flexible channel when you need to define your own data format for syncing data, triggering events, or implementing custom game logic.

#### sendMessage

Sends message to peers.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 2.01.00 PM.png" alt="" width="375"><figcaption></figcaption></figure>

#### onMessage

Receives message from peers.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 2.08.41 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### NetworkSync

This module synchronizes real-time position and entity state across players. This module is commonly used to update user or object positions in multiplayer scenes.

#### updateMyPosition

Sends my position request to the server.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 2.10.15 PM.png" alt="" width="375"><figcaption></figcaption></figure>

#### updateEntityPosition

Sends entity's position request to the server.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 2.12.01 PM.png" alt="" width="375"><figcaption></figcaption></figure>

#### onNotifyPositionUpdate

Triggered when a user or entity's position or state is updated in the scene.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 2.12.42 PM.png" alt="" width="375"><figcaption></figcaption></figure>

#### onNotifyRemove

Tirggered when a user or an entity is removed from the scene.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 2.13.33 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### ActionSync

This module handles action-level communication and arbitration between players. Use this module when multiple players may trigger the same event (e.g. interacting with an object), and only one action should take effect — a process known as competition.

#### competition

Sends an action request to the server for arbitration.

{% hint style="info" %}
All parameters must match across clients to be recognized as the same competition event.
{% endhint %}

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 2.15.35 PM.png" alt="" width="375"><figcaption></figcaption></figure>

#### onCompetition

Triggered when a competition result is returned from the server.

{% hint style="info" %}
Use this to determine which player won the arbitration and proceed accordingly in your game logic (e.g. only the winner picks up the item).
{% endhint %}

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 2.16.52 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### Real-time Leaderboard

This module handles real-time score reporting and live leaderboard updates across players during gameplay. Use this module to update a player's score and receive ranking changes in real time.

#### leaderboardUpdate

Submits a new score to the real-time leaderboard.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 2.19.02 PM.png" alt="" width="375"><figcaption></figcaption></figure>

#### onLeaderboardUpdate

Triggered when the leaderboard is updated and a new score ranking is available.

<figure><img src=".gitbook/assets/Screenshot 2025-06-12 at 2.19.56 PM.png" alt="" width="375"><figcaption></figcaption></figure>
