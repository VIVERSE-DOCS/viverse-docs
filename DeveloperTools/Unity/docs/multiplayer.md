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

## Quick how-to: matchmaking demo

1. Open **`MatchmakingDemoScene.unity`**.  
2. Set **App ID** in the demo UI (`MatchmakingDemoManager`).  
3. Use the UI to **connect**, **create/join** rooms, **start game** per sample.  
4. When transitioning to multiplayer, the sample saves room/session ids to **`PlayerPrefs`** (`MultiplayerRoomId`, `MultiplayerAppId`, `MultiplayerUserSessionId`, `ShouldAutoConnect`) and loads **`MultiplayerDemoScene`**.

Study **`MatchmakingDemoManager`** and **`MatchmakingClient`** for request/response shapes.

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
