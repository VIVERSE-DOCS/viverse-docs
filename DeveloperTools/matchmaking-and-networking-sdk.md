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

