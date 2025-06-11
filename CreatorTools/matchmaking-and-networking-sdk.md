---
description: >-
  This guide introduces the Play SDK and explains how to integrate core
  multiplayer features into VIVERSE  Studio content. It covers setup and usage
  for features such as matchmaking, session management,
---

# Matchmaking & Networking SDK

***

> **BEFORE GETTING STARTED:** you must [authenticate with VIVERSE](developer-tools/login-and-authentication-for-the-sdk.md), including App ID creation in VIVERSE Studio, before requesting Play SDK services.

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

<figure><img src=".gitbook/assets/image (728).png" alt=""><figcaption></figcaption></figure>

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

Create new room with custom settings.

<figure><img src=".gitbook/assets/image (729).png" alt=""><figcaption></figcaption></figure>

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

## Event Listeners

