---
name: viverse-unity-matchmaking
description: Unity Matchmaking — room lifecycle, actor management, real-time events via dual-path (WebGL viverse-sdk UMD + Editor NativeWebSocket).
prerequisites: [Unity 2021.2+, NativeWebSocket plugin, VIVERSE App ID, WebGL template with viverse-sdk CDN]
tags: [unity, matchmaking, viverse-sdk, rooms, webgl, multiplayer]
---

# VIVERSE Unity SDK — Matchmaking

Integrate matchmaking (room create/join/leave, actor identity, real-time room events) into Unity projects targeting WebGL and Editor.

## When To Use This Skill

Use when a Unity project needs:
- Online room creation and joining
- Player identity (SetActor) before room operations
- Real-time room list updates and actor change events
- Cross-tab/cross-client room discovery
- WebGL production builds with Editor development testing

## Architecture Overview

The SDK uses a **dual-path architecture**:
- **WebGL** (`#if UNITY_WEBGL && !UNITY_EDITOR`): C# calls jslib → jslib calls `new viverse.Play().newMatchmakingClient()` (viverse-sdk UMD loads play-sdk internally from CDN)
- **Editor** (`#if !UNITY_WEBGL || UNITY_EDITOR`): C# connects directly via NativeWebSocket to the matchmaking backend

C# is a **thin interface only** — zero business logic. All matchmaking logic lives in the JS SDK (WebGL) or the backend server (Editor).

**Key design**: WebGL loads ONLY viverse-sdk UMD from CDN (`index.umd.cjs`). No separate play-sdk script tag needed — viverse-sdk dynamically loads play-sdk at runtime when `new Play()` is constructed.

## Read Order

1. This file (workflow + compliance gates)
2. [patterns/dual-path-architecture.md](patterns/dual-path-architecture.md)
3. [patterns/matchmaking-flow.md](patterns/matchmaking-flow.md)

## Prerequisites

1. Unity 2021.2+ with .NET 4.x runtime
2. NativeWebSocket plugin in `Plugins/NativeWebSocket/`
3. App ID from [VIVERSE Studio](https://studio.viverse.com/)
4. For WebGL builds: custom WebGL template with viverse-sdk CDN loaded before Unity loader

## SDK File Structure

```
Assets/viverse-unity-sdk/
├── Runtime/MatchmakingClient.cs        # Dual-path MatchmakingClient (MonoBehaviour singleton)
├── Plugins/JSLib/ViversePlay.jslib     # WebGL bridge (uses new viverse.Play().newMatchmakingClient())
├── Plugins/NativeWebSocket/            # WebSocket plugin for Editor path
└── Sample/ViverseTestRunner.cs         # Reference implementation with matchmaking UI

Assets/WebGLTemplates/ViverseSDK/
└── index.html                          # Loads viverse-sdk CDN (single script, no play-sdk needed)
```

## Mandatory Compliance Gates (MUST PASS)

1. **MUST** call `Initialize(appId, debugMode)` before any other SDK method.
2. **MUST** call `SetActor` after receiving `OnConnect` event — server rejects all operations without actor identity.
3. **MUST NOT** manually generate session_id — `MatchmakingClient.Initialize()` auto-generates a GUID and injects it into every request.
4. **MUST** use PascalCase request types: `"SetActor"`, `"CreateRoom"`, `"JoinRoom"`, `"LeaveRoom"`, `"GetAvailableRooms"`, `"GetRoomProperties"`, `"CloseRoom"`, `"OpenRoom"`, `"StartGame"`, `"StartMatch"`, `"CancelMatch"`.
5. **MUST** subscribe to events before calling `Initialize` to avoid missing early events.
6. **MUST** discover rooms via `GetAvailableRooms` before joining — never use hardcoded room IDs.
7. **MUST NOT** add business logic to C# — all logic lives in jslib (WebGL) or server protocol (Editor).
8. **MUST NOT** load `play-sdk.umd.js` separately — viverse-sdk handles this internally.
9. **MUST** declare `__deps: ['$ViversePlay_State']` on every jslib function that accesses the shared state object.
10. **MUST** handle `OnDisconnect` and clean up pending requests — server may close connection on timeout.
11. **MUST** implement heartbeat (send `0x00` byte every 30s) in Editor path to prevent server disconnect.
12. **MUST** use `new (globalThis.viverse.Play)()` (with `new`) in jslib — the UMD exports the class, NOT a factory function.

## API Reference

### Events (subscribe via `+=`)

| Event | Signature | When |
|-------|-----------|------|
| `OnConnect` | `Action` | WebSocket connected |
| `OnDisconnect` | `Action` | WebSocket closed |
| `OnError` | `Action<string>` | Error occurred |
| `OnStateChange` | `Action<string>` | Connection state changed |
| `OnRoomListUpdate` | `Action<string>` | Room list broadcast |
| `OnJoinRoom` | `Action<string>` | Joined a room |
| `OnJoinedLobby` | `Action` | Returned to lobby |
| `OnRoomActorChange` | `Action<string>` | Room actors changed |
| `OnRoomClosed` | `Action` | Room was closed |
| `OnGameStartNotify` | `Action` | Game start signal |
| `OnMatchingTimeout` | `Action` | Match timed out |

### Methods

| Method | Description |
|--------|-------------|
| `Initialize(appId, debugMode)` | Connect to matchmaking service (generates session_id) |
| `Disconnect()` | Close connection |
| `SendRequest(requestType, jsonPayload, callback)` | Send matchmaking request |
| `GetCurrentActor()` | Get current actor JSON |
| `GetCurrentRoom()` | Get current room JSON |
| `IsConnected()` | Connection status |
| `IsInLobby()` | True if not in a room |
| `IsJoinedToRoom()` | True if in a room |

### Request Types and Payloads

```csharp
// SetActor — register identity (REQUIRED before any room operation)
// Note: session_id is auto-injected by SendRequest, include properties.name
SendRequest("SetActor", "{\"properties\":{\"name\":\"Player1\"}}", callback);

// CreateRoom
SendRequest("CreateRoom", "{\"room_name\":\"MyRoom\",\"max_players\":4,\"min_players\":2}", callback);

// JoinRoom
SendRequest("JoinRoom", "{\"room_id\":\"uuid-here\"}", callback);

// LeaveRoom
SendRequest("LeaveRoom", "{}", callback);

// GetAvailableRooms
SendRequest("GetAvailableRooms", "{}", callback);

// GetRoomProperties (must be in a room)
SendRequest("GetRoomProperties", "{}", callback);

// StartMatch (auto-matchmaking)
SendRequest("StartMatch", "{\"match_mode\":\"ranked\",\"min_players\":2,\"max_players\":4}", callback);

// CancelMatch
SendRequest("CancelMatch", "{}", callback);
```

### Response Format

All callbacks receive a JSON envelope:
```json
{"request_id":1,"success":true,"request_type":"GetAvailableRooms","data":[...rooms...]}
```
Parse `data` field for the actual payload. For room operations, `data` contains the room object. For `GetAvailableRooms`, `data` is an array of room objects.

## Typical Integration Flow

```csharp
using ViverseSDK;

// 1. Get or create singleton
var client = MatchmakingClient.Instance;
if (client == null)
{
    var go = new GameObject("[MatchmakingClient]");
    client = go.AddComponent<MatchmakingClient>();
}

// 2. Subscribe to events BEFORE Initialize
client.OnConnect += () => {
    // 3. SetActor here (required before room ops)
    client.SendRequest("SetActor", "{\"properties\":{\"name\":\"Player\"}}", null);
};
client.OnJoinRoom += (json) => { /* Room joined, start game */ };
client.OnRoomListUpdate += (json) => { /* Update room browser UI */ };
client.OnRoomActorChange += (json) => { /* Update player list */ };

// 4. Connect (session_id auto-generated)
client.Initialize("your-app-id", true);

// 5. After SetActor callback, do room operations
client.SendRequest("CreateRoom", "{\"room_name\":\"Test\",\"max_players\":4,\"min_players\":2}", null);
```

## WebGL Template Setup

Place in `Assets/WebGLTemplates/ViverseSDK/index.html`:
```html
<!-- viverse-sdk CDN MUST load before Unity loader. No play-sdk script needed. -->
<script src="https://www.viverse.com/static-assets/viverse-sdk/index.umd.cjs"></script>
<script src="Build/{{{ LOADER_FILENAME }}}"></script>
```

Set template in Player Settings → Resolution and Presentation → WebGL Template → "ViverseSDK".

## Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| "Class constructor ce cannot be invoked without 'new'" | Use `new (globalThis.viverse.Play)()` not `globalThis.viverse.play()` |
| "User's SessionID is required" | `Initialize()` auto-generates session_id; ensure it's being injected by `SendRequest` |
| "Unknown request type" | Use PascalCase: `"CreateRoom"` not `"createRoom"` |
| "Actor not set" | Call `SetActor` after `OnConnect` fires, before any room operation |
| Connection drops after ~30s | Heartbeat not implemented (Editor path sends `0x00` every 30s) |
| `_ViversePlay_State is not defined` | Missing `__deps: ['$ViversePlay_State']` in jslib function |
| Nested JSON stripped | Don't use `Trim('{', '}')` — use `Substring(1, len-2)` for outer braces only |
| GUI disappears after reconnect | Clear singleton Instance in OnDestroy |
| `IsConnected()` always true (WebGL) | Ensure `_isConnected` tracks `onConnect`/`onDisconnect` events |

## Server Protocol (Editor Path)

- URL: `wss://broadcasting-gateway-gaming.vrprod.viveport.com/api/matchmaking-service/v1/match?app_id={appId}&debug=true`
- Format: JSON over WebSocket (debug mode) or XOR-encrypted binary (production)
- Heartbeat: Send single byte `0x00` every 30 seconds
- Server broadcasts: `request_type` field indicates response type (e.g., `"BroadcastRoomList"`, `"GetRoomActors"`)
- Events from jslib path: `{"event":"eventName","data":{...}}` format

## SendRequest Wire Format

`SendRequest()` automatically wraps your payload with protocol fields:
```json
{
  "request_id": 1,
  "request_type": "CreateRoom",
  "app_id": "your-app-id",
  "session_id": "auto-generated-guid",
  "room_name": "MyRoom",
  "max_players": 4,
  "min_players": 2
}
```

You only provide the inner fields (`room_name`, `max_players`, etc.) — `request_id`, `request_type`, `app_id`, and `session_id` are injected automatically.
