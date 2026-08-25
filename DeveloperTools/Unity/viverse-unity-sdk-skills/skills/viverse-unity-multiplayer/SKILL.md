---
name: viverse-unity-multiplayer
description: Unity Play SDK Multiplayer — real-time data channels, 6 game modules, master/client architecture via dual-path (WebGL Mediasoup + Editor WebRTC proxy).
prerequisites: [Unity 2021.2+, NativeWebSocket plugin, VIVERSE App ID, WebGL template with viverse-sdk CDN + play-sdk.umd.js, Active room from MatchmakingClient]
tags: [unity, multiplayer, play-sdk, webrtc, mediasoup, modules, realtime, webgl]
---

# Play Unity SDK — Multiplayer

Integrate real-time multiplayer (data channels, game modules, master/client) into Unity projects targeting WebGL and Editor. Requires an active room from MatchmakingClient.

## When To Use This Skill

Use when a Unity project needs:
- Real-time data exchange between players (position sync, actions, scores)
- Game lifecycle management (countdown, ready, end)
- Network synchronization of object positions
- Competitive action broadcasting (ActionSync)
- In-game leaderboards via multiplayer channel
- Lambda serverless function invocation
- Master/client authority model

## Architecture Overview

The SDK uses a **dual-path architecture**:
- **WebGL** (`#if UNITY_WEBGL && !UNITY_EDITOR`): C# calls jslib → jslib uses TypeScript SDK's `MultiplayerClient` which manages Mediasoup WebRTC data channels
- **Editor** (`#if !UNITY_WEBGL || UNITY_EDITOR`): C# connects via NativeWebSocket to a WebRTC proxy service that bridges WebSocket ↔ Mediasoup data channel

C# is a **thin interface only** — zero WebRTC logic, zero media handling. All multiplayer logic lives in the JS SDK (WebGL) or the proxy service (Editor).

## Dependency

**Requires `viverse-unity-matchmaking` skill** — you must have a `roomId` from MatchmakingClient before using MultiplayerClient.

## Read Order

1. This file (workflow + compliance gates)
2. [patterns/multiplayer-lifecycle.md](patterns/multiplayer-lifecycle.md)
3. [patterns/module-messaging.md](patterns/module-messaging.md)

## Prerequisites

1. Unity 2021.2+ with .NET 4.x runtime
2. NativeWebSocket plugin in `Plugins/NativeWebSocket/`
3. App ID from [VIVERSE Studio](https://studio.viverse.com/)
4. WebGL template with viverse-sdk CDN + `play-sdk.umd.js` loaded before Unity loader
5. An active room obtained via MatchmakingClient (roomId)

## SDK File Structure

```
Assets/viverse-unity-sdk/
├── Runtime/
│   ├── MultiplayerClient.cs           # Dual-path client (MonoBehaviour singleton)
│   ├── MultiplayerInitOptions.cs      # Module enablement config
│   ├── MiniJson.cs                    # Lightweight JSON (no external deps)
│   └── Modules/
│       ├── GameModule.cs              # Lifecycle: countdown, ready, end
│       ├── NetworkSyncModule.cs       # Position/transform sync
│       ├── ActionSyncModule.cs        # Competitive action broadcast
│       ├── LeaderboardModule.cs       # In-game score tracking
│       ├── LambdaModule.cs            # Serverless function calls
│       └── GeneralModule.cs           # Raw message send/receive
├── Plugins/JSLib/PlaySDK.jslib        # WebGL bridge (Multiplayer + Lambda functions)
├── Plugins/NativeWebSocket/           # WebSocket plugin for Editor path
└── Sample/MultiplayerDemo.cs          # Reference implementation

Assets/WebGLTemplates/PlaySDK/
├── index.html                         # Loads viverse-sdk CDN + play-sdk.umd.js
└── play-sdk.umd.js                    # Play SDK UMD (temporary until jslib migration)
```

## Mandatory Compliance Gates (MUST PASS)

1. **MUST** obtain a `roomId` from MatchmakingClient before using MultiplayerClient.
2. **MUST** call `Initialize(roomId, appId, userSessionId)` before any other multiplayer method.
3. **MUST** subscribe to `OnConnected` before calling `Initialize` — connection may complete instantly (WebGL).
4. **MUST** call `Init(options)` after `OnConnected` fires to create the game room and activate modules.
5. **MUST** configure modules via `MultiplayerInitOptions` if using Lambda, NetworkSync, etc.
6. **MUST** use module message format: `{"source":"client","messageType":"bot","type":"<module>","event":"<event>","user_id":"<peerId>"}`.
7. **MUST** filter self-originated messages in module handlers (compare `user_id` to `PeerId`).
8. **MUST NOT** add WebRTC logic, state machines, or business logic to C# — all lives in jslib/proxy.
9. **MUST NOT** use `JsonUtility` for complex nested module messages — use MiniJson.
10. **MUST** declare `__deps: ['$PlaySDK_State']` on every jslib function accessing shared state.
11. **MUST** handle `OnDisconnected` and clean up module state.
12. **MUST** set `Instance = null` in `OnDestroy` for reconnection support.
13. **MUST** use `#pragma warning disable CS4014` for fire-and-forget WebSocket operations (Editor path).

## API Reference

### MultiplayerClient (Singleton MonoBehaviour)

#### Properties

| Property | Type | Description |
|----------|------|-------------|
| `Instance` | `MultiplayerClient` | Singleton accessor |
| `RoomId` | `string` | Current room ID |
| `AppId` | `string` | Application ID |
| `PeerId` | `string` | Local peer identifier |

#### Events

| Event | Signature | When |
|-------|-----------|------|
| `OnConnected` | `Action` | Data channel / proxy connected |
| `OnDisconnected` | `Action` | Connection closed |
| `OnClientConnected` | `Action<string>` | Remote peer joined |
| `OnClientDisconnected` | `Action<string>` | Remote peer left |
| `OnMessage` | `Action<string>` | Raw message received |

#### Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `Initialize(roomId, appId, userSessionId)` | `Task` | Connect to multiplayer service |
| `Init(options)` | `Task<string>` | Create game room, activate modules |
| `Send(data)` | `void` | Send raw string data |
| `Disconnect()` | `void` | Close connection |
| `SetMaster(bool)` | `void` | Set local master authority |
| `IsMasterUser()` | `bool` | Check if local is master |
| `GetRoomInfo()` | `Task<string>` | Get room metadata |
| `IsInitialized()` | `bool` | Check if Init() was called |

### Modules

#### GameModule

| Event/Method | Description |
|---|---|
| `OnBotLeave` | Bot left the room |
| `OnMasterNotify` | Master assignment notification |
| `OnWaitForPlayer` | Waiting for more players |
| `OnPlayerAllReady` | All players ready |
| `OnPlayerOverLimit` | Too many players |
| `OnCountdownToStart` | Game starting countdown |
| `OnCountdownToEnd` | Game ending countdown |
| `OnGameTimeUp` | Game time expired |
| `OnGameEnd` | Game ended |
| `OnGameRestart` | Game restarting |
| `OnErrorNotify` | Error from server |
| `GetRoomInfoInternal()` | Internal room info request |

#### NetworkSyncModule

| Event/Method | Description |
|---|---|
| `OnNotifyPositionUpdate` | Position data from other players |
| `OnNotifyRemove` | Object removed |
| `SendPosition(json)` | Broadcast position update |
| `SendRemove(objectId)` | Broadcast object removal |

#### ActionSyncModule

| Event/Method | Description |
|---|---|
| `OnCompetition` | Competition action from other players |
| `SendAction(json)` | Broadcast action |

#### LeaderboardModule

| Event/Method | Description |
|---|---|
| `OnLeaderboardUpdate` | Score update from server |
| `SubmitScore(json)` | Submit score |

#### LambdaModule

| Event/Method | Description |
|---|---|
| `CreateJob(gameId, eventName, data, requestId, token)` | `Task<string>` — invoke serverless function |
| `GetJobStatus(jobId, token)` | `Task<string>` — check job result |
| `SetEnabled(bool)` | Enable/disable Lambda module |

#### GeneralModule

| Event/Method | Description |
|---|---|
| `OnMessage` | Raw message received (non-module) |
| `Send(json)` | Send raw JSON |

## MultiplayerInitOptions

```csharp
var options = new MultiplayerInitOptions
{
    modules = new ModulesConfig
    {
        lambda = new LambdaConfig { enabled = true }
    }
};
await multiplayerClient.Init(options);
```

## Typical Integration Flow

```csharp
// 1. Get roomId from matchmaking (prerequisite)
string roomId = /* from MatchmakingClient.OnJoinRoom */;

// 2. Get or create MultiplayerClient
var go = new GameObject("MultiplayerClient");
var mp = go.AddComponent<MultiplayerClient>();

// 3. Subscribe events BEFORE Initialize
mp.OnConnected += async () =>
{
    // 4. Init game room after connected
    var result = await mp.Init(new MultiplayerInitOptions());
    Debug.Log($"Game room created: {result}");
};

mp.OnClientConnected += (peerId) => Debug.Log($"Player joined: {peerId}");
mp.OnDisconnected += () => Debug.Log("Disconnected");

// 5. Subscribe module events
mp.NetworkSync.OnNotifyPositionUpdate += (json) => { /* update transforms */ };
mp.Game.OnCountdownToStart += (json) => { /* show countdown UI */ };
mp.ActionSync.OnCompetition += (json) => { /* handle action */ };

// 6. Connect
await mp.Initialize(roomId, "your-app-id", "unique-user-session-id");

// 7. During gameplay — send position updates
mp.NetworkSync.SendPosition("{\"x\":1,\"y\":2,\"z\":3,\"objectId\":\"player1\"}");

// 8. Send competitive action
mp.ActionSync.SendAction("{\"action\":\"attack\",\"target\":\"enemy1\",\"damage\":10}");

// 9. Use Lambda for server-side logic
var jobResult = await mp.Lambda.CreateJob("game-id", "calculate-reward", "{\"score\":100}", "req-1", "auth-token");

// 10. Disconnect when done
mp.Disconnect();
```

## WebGL vs Editor Path Details

### WebGL Path
- jslib creates `new window.play.MultiplayerClient(roomId, appId, userSessionId)`
- Mediasoup WebRTC handles data channel setup internally
- Events forwarded via `SendMessage(gameObjectName, "ReceiveMessageFromJS", json)`
- Lambda calls go through jslib → TypeScript SDK → REST API

### Editor Path
- NativeWebSocket connects to: `wss://broadcasting-gateway-gaming.vrprod.viveport.com/api/webrtcproxy-service/v1/ws?roomId={roomId}&peerId={peerId}&pod=0`
- Proxy bridges WebSocket ↔ Mediasoup data channel on server side
- Module messages received directly as JSON via WebSocket
- Lambda calls go through LambdaModule → REST API directly (not via proxy)

## Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| "No roomId" | Must get roomId from MatchmakingClient first (join/create room) |
| Init() called before connected | Wait for OnConnected event before calling Init() |
| Module events not firing | Ensure Init() was called with correct module options |
| Self-messages echoed | Filter by `user_id != PeerId` in module handlers |
| `PlaySDK_State is not defined` | Missing `__deps: ['$PlaySDK_State']` in jslib |
| Lambda timeout | Check token validity and Lambda function deployment |
| Editor path no messages | Verify proxy URL is reachable and roomId matches active room |
| Singleton destroyed on scene load | `DontDestroyOnLoad(gameObject)` is set — ensure only one instance |
| WebRTC data-channel connect timeout in WebGL | **Server-side issue (RESOLVED).** Mediasoup Worker previously announced hostname `broadcasting-gateway.viverse.com` in ICE candidates; round-robin DNS routed UDP to the wrong host. Fix (server-side): set `announcedIp` in mediasoup Worker config to the pod's public IP. **No client SDK change required.** If this reproduces, contact the VIVERSE Play team. |

## Module Deep-Dive Skills

For focused work on a single module, use the per-module skills instead of this bootstrap skill:

- `viverse-unity-game-module` — Lifecycle, countdown, ready, end, restart
- `viverse-unity-network-sync` — Continuous position/transform sync
- `viverse-unity-action-sync` — Discrete competitive action broadcasts
- `viverse-unity-leaderboard-module` — Real-time in-room score sync
- `viverse-unity-general-module` — Freeform peer messaging

This skill (`viverse-unity-multiplayer`) covers the shared bootstrap (Initialize → Init → module access). Use it first; then load a module-specific skill for detailed patterns.

## Module Message Protocol

### Outbound (client → server/peers)

```json
{
  "source": "client",
  "messageType": "bot",
  "type": "network_sync",
  "event": "position_update",
  "user_id": "<PeerId>",
  "data": { /* module-specific payload */ }
}
```

### Inbound (server/peers → client)

```json
{
  "source": "bot",
  "messageType": "bot",
  "type": "network_sync",
  "event": "notify_position_update",
  "user_id": "<sender-peerId>",
  "data": { /* module-specific payload */ }
}
```

Module types: `"game"`, `"network_sync"`, `"action_sync"`, `"leaderboard"`, `"lambda"`, `"general"`

> Note: The outbound `type` values in the JSON envelope use `snake_case` (`network_sync`, `action_sync`). The C# property accessors on `MultiplayerClient` use `PascalCase` (`mp.NetworkSync`, `mp.ActionSync`), and the internal `jslib` event bridge routes on `networksync/*` and `actionsync/*` (no underscore). Only the wire-protocol `type` field uses underscores.
