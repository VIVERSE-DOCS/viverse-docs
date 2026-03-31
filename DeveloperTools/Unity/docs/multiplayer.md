# Multiplayer (Play SDK)

## Who this is for

Developers using the **Play SDK example** package for **lobby matchmaking** and **realtime multiplayer** in Unity (especially **WebGL** builds running inside Viverse).

**Package:** Play example (e.g. `1215_playSDK_example.unitypackage`).  
**Sample scenes:**  
- `Assets/Scenes/MatchmakingDemoScene.unity`  
- `Assets/Scenes/MultiplayerDemoScene.unity`

---

## Architecture in one minute

| Stage | Client | Role |
|-------|--------|------|
| Lobby | `MatchmakingClient` | Regions, create/join room, actors, matchmaking |
| Game | `MultiplayerClient` | WebSocket-style session, modules, custom messages |

On **WebGL**, both clients talk to JavaScript via **`DllImport("__Internal")`** implemented in **`Assets/Plugins/WebGL/play-sdk.jslib`**, which loads **`globalThis.viverse.play`** and constructs Play clients.

---

## Lobby (matchmaking)

**Problem:** Players need to discover rooms, create one, or join an existing room before the realtime game session.

**SDK approach:** Use **`MatchmakingClient`** (`Assets/Scripts/PlaySDK/MatchmakingClient.cs`). The sample scene **`MatchmakingDemoScene`** wires the same flow in **`MatchmakingDemoManager`**.

### 1. Create and initialize the client

Add `MatchmakingClient` to a GameObject, then call **`Initialize`** with your App ID (from VIVERSE Studio):

```csharp
var go = new GameObject("MatchmakingClientInstance");
var client = go.AddComponent<MatchmakingClient>();
client.Initialize(appId, debugMode: false);
```

See the connect handler in **`MatchmakingDemoManager`** (creates the GameObject, adds the component, calls `Initialize`). Subscribe to **`OnConnect`** so you enable lobby UI only after the service link is up. The demo also uses **`OnJoinRoom`**, **`OnRoomListUpdate`**, **`OnDisconnect`**, **`OnError`**, and related events.

### 2. Set the local player (actor)

Before creating or joining a room, define an **`Actor`** (name, `session_id`, optional properties) and call **`SetActor`**:

```csharp
var actor = new Actor
{
    name = playerName,
    session_id = sessionId
    // optional: properties, team, etc.
};
var setActorResponse = await matchmakingClient.SetActor(actor);
```

Reference: **`MatchmakingDemoManager.HandleSetActorClick`**.

### 3. Refresh the room list

Request available rooms and read **`MatchResponse.rooms`**:

```csharp
var response = await matchmakingClient.GetAvailableRooms();
if (response?.rooms != null)
{
    foreach (var room in response.rooms)
    {
        // populate UI, e.g. dropdown
    }
}
```

Reference: **`MatchmakingDemoManager.HandleRefreshRoomsClick`**.

The client can also push updates via **`OnRoomListUpdate`**; the demo keeps **`_roomList`** in sync and refreshes the join dropdown.

### 4. Create a new room

**`CreateRoom`** takes a display **name**, a **mode** string, **max** and **min** players:

```csharp
matchmakingClient.CreateRoom(
    "My room",
    "Room",
    maxPlayers: 4,
    minPlayers: 2);
```

Reference: **`MatchmakingDemoManager.HandleCreateRoomClick`**. Successful create/join is reflected in **`OnJoinRoom`**, which receives the current **`Room`**.

### 5. Join an existing room

Use the room’s **`id`** from your UI (e.g. selected row in the refreshed list):

```csharp
matchmakingClient.JoinRoom(roomId);
```

Reference: **`MatchmakingDemoManager.HandleJoinRoomClick`**.

### 6. Leave the current room

```csharp
await matchmakingClient.LeaveRoom();
```

Reference: **`MatchmakingDemoManager.HandleLeaveRoomClick`**.

### 7. Hand off to `MultiplayerClient`

When you move to gameplay, use the same **game session**, **app id**, and **actor session id** that matchmaking used. The demo writes **`MultiplayerRoomId`**, **`MultiplayerAppId`**, **`MultiplayerUserSessionId`**, and **`ShouldAutoConnect`** to **`PlayerPrefs`**, then loads **`MultiplayerDemoScene`** (see **`LoadMultiplayerScene`** / **`LoadSceneAsync`** in **`MatchmakingDemoManager`**). Your game can use **`PlayerPrefs`**, a **ScriptableObject**, or a **bootstrap singleton** instead—the requirement is **one consistent triple** of ids between lobby and game.

---

## Quick how-to: matchmaking demo

1. Open **`MatchmakingDemoScene.unity`**.  
2. Set **App ID** in the demo UI (`MatchmakingDemoManager`).  
3. Use the UI to **connect**, **set actor**, **refresh rooms**, **create** or **join**, then **start game** as needed.  
4. When transitioning to multiplayer, the sample saves ids to **`PlayerPrefs`** and loads **`MultiplayerDemoScene`**.

For API-level steps, see **[Lobby (matchmaking)](#lobby-matchmaking)** above. Full request/response behavior: **`MatchmakingDemoManager`** and **`MatchmakingClient`**.

---

## Quick how-to: multiplayer demo

1. Open **`MultiplayerDemoScene.unity`** (or arrive from matchmaking with auto-connect).  
2. Connect with **room id**, **app id**, **user session id** consistent with the lobby.  
3. Call **`MultiplayerClient.Initialize`**, then **`Init(MultiplayerInitOptions)`** with enabled modules.

See **`MultiplayerDemoManager`** for subscriptions:

- `OnConnected` / `OnDisconnected`  
- `OnMessage`  
- `NetworkSync`, `ActionSync`, `Leaderboard`, `Game` events  

---

## Handoff pattern (lobby → game)

Your production game should pass the same identifiers the demos use:

- **Room / game session** — e.g. `_currentRoom.game_session` from matchmaking  
- **App ID** — your Play app id  
- **User session id** — actor `session_id` from matchmaking  

Persist between scenes with **`PlayerPrefs`**, a **ScriptableObject**, or a **static bootstrap**—the demos use `PlayerPrefs` for simplicity.

---

## Game flow after the lobby

The **[Lobby (matchmaking)](#lobby-matchmaking)** section covers finding or creating a room (Phase 1). The steps below map the rest of a typical session to **`MultiplayerDemoManager`** / **`MatchmakingDemoManager`** in the Play example package—**no** custom game project required to follow along. Where the demo only logs behavior, this doc notes what **your** game would add.

### Phase 2: Connecting to the game room

**Problem:** After matchmaking, the gameplay scene must use the **same** room, app id, and user session and enable **`MultiplayerClient`** with the correct modules.

**What the example does:** In **`MultiplayerDemoManager.HandleConnectMultiplayerClick`**, a GameObject is created (e.g. named for the WebGL bridge), **`MultiplayerClient`** is added, then:

1. **`await _multiplayerClient.Initialize(roomId, appId, userSessionId)`** — sets up transport (WebGL **JavaScript** bridge or Editor / **Mediasoup** proxy path).  
2. Subscriptions for **`OnConnected`**, **`OnDisconnected`**, **`OnClientConnected`**, **`OnClientDisconnected`**, **`OnMessage`**, and module events.  
3. **`await _multiplayerClient.Init(BuildInitOptionsFromUI())`** — registers enabled modules (**game**, **network_sync**, **action_sync**, **leaderboard**) and returns room / app / session info for the UI.

**Handoff from lobby:** **`MatchmakingDemoManager`** writes **`MultiplayerRoomId`**, **`MultiplayerAppId`**, **`MultiplayerUserSessionId`**, and **`ShouldAutoConnect`** to **`PlayerPrefs`**. **`MultiplayerDemoScene`** reads them in **`LoadSavedDataAndAutoConnect`** and triggers **`HandleConnectMultiplayerClick`**.

**Your game:** Keep one consistent triple **(`game_session`, app id, actor `session_id`)** from matchmaking into **`Initialize`**. Replace **`PlayerPrefs`** with a **ScriptableObject**, static bootstrap, or your own small handoff type if you prefer.

### Phase 3: Player discovery and spawning

**Problem:** Clients must know who is already in the room and react when peers join or leave.

**What the example does:**

- **`OnClientConnected`** / **`OnClientDisconnected`** — in the demo, these **append logs** only.  
- **`Game.GetRoomInfo()`** — **`HandleGetRoomInfoClick`** fetches room payload and logs JSON.

**What your game adds:** Call **`await _multiplayerClient.Game.GetRoomInfo()`** after **`Init`**, normalize whatever fields the payload uses (`user_ids`, `members`, etc.), spawn or update avatars. On **`OnClientConnected`**, spawn late joiners; on **`OnClientDisconnected`**, remove them.

### Phase 4: Master client and authority

**Problem:** Many titles need one **authoritative** client for starts, scoring, or world events.

**What the SDK provides:**

- **`GameModule`** can raise **`OnMasterNotify`** and call **`MultiplayerClient.SetMaster`** when the master user is resolved — see **`Assets/Scripts/PlaySDK/Modules/GameModule.cs`**.  
- **`MultiplayerClient.IsMasterUser()`** and **`SetMaster`** — WebGL delegates to **`play-sdk.jslib`**; non-WebGL tracks an internal flag updated from **`SetMaster`**.

**What the example omits:** **`MultiplayerDemoManager`** does not subscribe to **`OnMasterNotify`**. In your code:

```csharp
_multiplayerClient.Game.OnMasterNotify += (data) =>
{
    // Parse master_user from data; gate host-only actions
};
```

Use **`IsMasterUser()`** (and/or **`OnMasterNotify`**) to guard **`TriggerGameStart`**, **ActionSync** broadcasts, or **`SendMessage`** routing.

### Phase 5: Ready phase and game lifecycle

**Problem:** Ready gates, countdowns, play timers, and restarts should follow the **server-side game module** when **`game`** is enabled.

**What the example does:** With **`enableGameModule`**, **`BuildInitOptionsFromUI`** sets **`ready_time`**, **`start_delay_time`**, **`play_time`**, **`total_player`**, **`min_total_player`**, **`max_total_player`**, **`wait_player_timeout`**, **`change_second`**, and related fields. The demo subscribes to **`Game.OnCountdownToStart`**, **`OnCountdownToEnd`**, **`OnGameEnd`**, **`OnGameRestart`**, **`OnGameTimeUp`**, **`OnErrorNotify`**, and exposes UI for **`TriggerGameStart`**, **`TriggerGameEnd`**, **`TriggerGameUpdate`**, **`TriggerGameRestart`**.

**Your game:** Call **`_multiplayerClient.Game.Ready()`** when the local player is ready (not exposed as a demo button—use **`GameModule`** from code). In production, usually **only the master** calls **`TriggerGameStart`** / **`TriggerGameRestart`**; the demo buttons are for learning.

### Phase 6: Position sync and custom messages

**Problem:** You need either **continuous** state (positions) or **arbitrary** payloads (chat, custom game state).

**What the example shows:**

- **`NetworkSyncModule`** — **`UpdateMyPosition`** / **`UpdateEntityPosition`** with demo data; subscribe to **`OnNotifyPositionUpdate`** / **`OnNotifyRemove`**.  
- **`GeneralModule`** — **`MultiplayerClient.SendMessage`** (demo uses a chat **string**); **`OnMessage`** logs incoming traffic.

**Full games** often send **JSON-serializable dictionaries** via **`SendMessage`** (e.g. an **`action`** field + **`userId`** + payload) and apply **host-only** handling on **`OnMessage`**, or rely on **`NetworkSync`** for pose-style updates instead of reinventing a stream on the general channel.

### Phase 7: Discrete events (ActionSync)

**Problem:** One-off gameplay events (pickups, flags, round results) fit **`ActionSync`** better than a raw position stream.

**What the example does:** **`HandleSendCompetitionClick`** calls:

```csharp
_multiplayerClient.ActionSync.Competition(actionName, actionMsg, actionId);
```

using UI fields; **`OnCompetition`** logs the result.

**Your game:** Define stable **`actionName`** strings, serialize structured data in **`actionMsg`** (often JSON), and use distinct **`actionId`** values when you need deduplication. You may **skip** handling events you already applied locally for the same **`session_id`** (common pattern in larger titles).

### Phase 8: Leaderboard (in session)

**Problem:** Publish scores for the **current Play session**.

**What the example does:** **`HandleUpdateLeaderboardClick`** calls **`Leaderboard.LeaderboardUpdate(score)`**; **`OnLeaderboardUpdate`** logs.

This is the Play **`LeaderboardModule`**, not the **HTTP leaderboard sample** in the Viverse Unity SDK package (`LeaderboardService` / **`Login_Leaderboard_Sample`**). See [Leaderboards](leaderboards.md) for the REST-style sample.

### Phase 9: Teardown and cleanup

**Problem:** Release network resources and avoid **stale handoff** when exiting.

**What the example does:** **`MultiplayerDemoManager.OnDestroy`** calls **`_multiplayerClient?.Disconnect()`** and **`CleanupSavedData()`** (clears **`PlayerPrefs`** keys used for lobby handoff). The lobby demo calls **`MatchmakingClient.Disconnect()`** when reconnecting or tearing down.

**Your game:** Disconnect when unloading the gameplay scene and clear whatever store you used for **game_session** / **app id** / **session_id** so the next entry does not auto-connect with old values.

### Phases 2–9 vs shipped demo (summary)

| Phase | Topic | Demonstrated in `MultiplayerDemoManager` | You typically add |
|-------|--------|------------------------------------------|-------------------|
| 2 | Connect + **`Init`** | Yes | Stable id handoff |
| 3 | Discovery / spawn | **`GetRoomInfo`**, join/leave **logs** only | Parse room info, spawn avatars |
| 4 | Master | Not wired in demo | **`OnMasterNotify`**, **`IsMasterUser`** |
| 5 | Game lifecycle | Yes (if **game** module + UI) | **`Ready()`**, master-gated starts |
| 6 | Sync / messages | **`NetworkSync`** + chat **`SendMessage`** | Optional host-authoritative JSON |
| 7 | ActionSync | Yes | Your action names + routing |
| 8 | Leaderboard | Yes | When to update scores |
| 9 | Teardown | **`Disconnect`** + **`CleanupSavedData`** | Same pattern in your scenes |

---

## Modules (`MultiplayerInitOptions`)

Enable and configure modules in **`MultiplayerInitOptions`** (see **`MultiplayerDemoManager.BuildInitOptionsFromUI`**):

| Module | Typical use |
|--------|-------------|
| **game** | Ready, countdown, timers, restart |
| **network_sync** | Position / entity sync |
| **action_sync** | Named competitions |
| **leaderboard** | In-session score updates |
| **general** | Custom payloads via `SendMessage` / `OnMessage` |

---

## Custom messages

Use **`MultiplayerClient.SendMessage(string)`** and **`GeneralModule`** / `OnMessage` handlers to implement your own **host-authoritative** or **P2P-style** protocols (JSON dictionaries are common).

---

## Non-WebGL

Editor and standalone builds may use different transports (e.g. managed WebSocket, proxy client) as implemented in **`MatchmakingClient`** / **`MultiplayerClient`** — read the `#if UNITY_WEBGL` regions in source.

---

## Configuration

Service URLs: [Configuration](configuration.md) — `EnvConfig`, `.env`, multiplayer proxy WebSocket URL.

---

## See also

- [Overview](overview.md) — two-package split  
- [Login](login.md) — if you unify user identity with Viverse login  
- [Troubleshooting](troubleshooting.md)  
