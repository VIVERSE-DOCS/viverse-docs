---
name: viverse-unity-game-module
description: Unity Play SDK Game Module — drives multiplayer game lifecycle (ready, countdown, start, end, restart) through the GameModule property on MultiplayerClient.
prerequisites: [Unity 2021.2+, App ID from VIVERSE Studio, MultiplayerClient initialized via viverse-unity-multiplayer skill]
tags: [unity, multiplayer, play-sdk, module, webgl, editor, game-lifecycle, countdown]
---

# Play Unity SDK — Game Module

Drive the full multiplayer game lifecycle (ready, countdown, start, end, restart) through the server-authoritative `GameModule`.

## When To Use This Skill

Use when a Unity project needs:
- Player "ready" handshake before a match starts
- Server-counted countdown before game starts or ends
- Detection of when all players are ready
- Master-authority game-start and game-end triggers
- Game restart after a match ends
- Room info queries during a session

## Architecture Overview

The dual-path architecture applies exactly as in the parent `viverse-unity-multiplayer` skill:

- **WebGL**: C# calls jslib via `DllImport` -> jslib notifies the TypeScript SDK's `MultiplayerClient` -> events arrive via `SendMessage(gameObjectName, "ReceiveMessageFromJS", json)` -> `MultiplayerClient.DispatchEvent` routes them to `GameModule.TriggerEvent`.
- **Editor**: NativeWebSocket proxy delivers JSON frames -> `MultiplayerClient.HandleProxyMessage` routes messages with `type:"game"` to `GameModule.HandleModuleMessage`.

C# is a thin interface only. All game lifecycle logic lives in the server-side bot. `GameModule` is a routing shim.

## Dependency

Requires both `viverse-unity-matchmaking` and `viverse-unity-multiplayer` skills. You must have a `roomId` from matchmaking, and `MultiplayerClient.Initialize` + `Init` must have completed successfully before calling any `GameModule` method.

## Read Order

1. This file (API + compliance gates)
2. [patterns/game-module-pattern.md](patterns/game-module-pattern.md)

## Prerequisites

1. Unity 2021.2+ with .NET 4.x runtime
2. App ID from [VIVERSE Studio](https://studio.viverse.com/)
3. Active room obtained via `MatchmakingClient` (roomId)
4. `MultiplayerClient.Initialize(roomId, appId, sessionId)` called and awaited
5. `MultiplayerClient.Init(options)` called with `modules.game.enabled = true`

## Module Access

```csharp
// After Initialize + Init complete, access the module via the singleton
var mp = MultiplayerClient.Instance;
GameModule game = mp.Game;

// Enable game module in Init options
var options = new MultiplayerInitOptions
{
    modules = new ModulesConfig
    {
        game = new ModuleOption
        {
            enabled = true,
            play_time = 60,          // seconds the game runs
            total_player = 4,
            min_total_player = 2,
            max_total_player = 4,
            ready_time = 3,          // countdown before game starts
            wait_player_timeout = 100
        }
    }
};
await mp.Init(options);
```

## API Reference

### Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `Ready()` | `void` | Send `client_ready` to server. Call once after connecting. Server auto-calls this when the bot detects the player in the user list. |
| `TriggerGameStart()` | `void` | Send `countdown_to_start` to server (master only). Kicks off the pre-game countdown. |
| `TriggerGameEnd()` | `void` | Send `game_end` to server (master only). Forces the game to end. |
| `TriggerGameUpdate(int totalPlayer)` | `void` | Send `game_update` with updated player count (master only). |
| `TriggerGameRestart()` | `void` | Send `game_restart` to server (master only). Resets master state and triggers a new ready cycle. |

> `GetRoomInfoInternal()` is an internal async method used by `MultiplayerClient.GetRoomInfo()` on the Editor path. Do not call it directly.

### Events

| Event | Signature | When |
|-------|-----------|------|
| `OnBotLeave` | `Action` | The server bot left the room |
| `OnMasterNotify` | `Action<string>` | Master assignment changed; JSON contains `master_user` |
| `OnWaitForPlayer` | `Action<string>` | Server is waiting for more players to join |
| `OnPlayerAllReady` | `Action<string>` | All connected players sent `client_ready` |
| `OnPlayerOverLimit` | `Action` | Player count exceeded `max_total_player` |
| `OnCountdownToStart` | `Action<string>` | Countdown before game starts; JSON contains remaining seconds |
| `OnCountdownToEnd` | `Action<string>` | Countdown before game ends; JSON contains remaining seconds |
| `OnGameTimeUp` | `Action` | `play_time` elapsed; game time expired |
| `OnGameEnd` | `Action` | Game ended (triggered by server or master) |
| `OnGameRestart` | `Action` | Game restarting; reset local state and wait for next `client_ready` cycle |
| `OnErrorNotify` | `Action<string>` | Server-side error; JSON contains error details |

## Message Protocol

### Outbound (client to server/peers)

```json
{
  "source": "client",
  "messageType": "bot",
  "type": "game",
  "event": "client_ready",
  "user_id": "<PeerId>"
}
```

Other outbound events: `countdown_to_start`, `game_end`, `game_update` (adds `total_player`), `game_restart`, `get_room_info` (adds `request_id`).

### Inbound (server/bot to client)

```json
{
  "source": "bot",
  "messageType": "bot",
  "type": "game",
  "event": "master_notify",
  "user_id": "<server-bot-id>",
  "master_user": "<masterPeerId>"
}
```

Full event list: `bot_ready`, `bot_leave`, `master_notify`, `wait_for_player`, `player_all_ready`, `player_over_limit`, `countdown_to_start`, `countdown_to_end`, `game_time_up`, `game_end`, `game_restart`, `error_notify`.

## Typical Integration Flow

```csharp
using System;
using UnityEngine;
using ViverseSDK;

public class GameController : MonoBehaviour
{
    private MultiplayerClient _mp;
    private bool _isMaster;

    async void Start()
    {
        // 1. Bootstrap is done by viverse-unity-multiplayer skill.
        //    This code runs after Initialize + Init completed.
        _mp = MultiplayerClient.Instance;

        // 2. Subscribe to GameModule events BEFORE calling Ready().
        _mp.Game.OnMasterNotify += OnMasterNotify;
        _mp.Game.OnWaitForPlayer += (json) => UpdateStatus("Waiting for players...");
        _mp.Game.OnPlayerAllReady += OnAllReady;
        _mp.Game.OnPlayerOverLimit += () => Debug.LogWarning("[Game] Room full");
        _mp.Game.OnCountdownToStart += (json) => ShowCountdown("Game starts in...", json);
        _mp.Game.OnCountdownToEnd += (json) => ShowCountdown("Game ends in...", json);
        _mp.Game.OnGameTimeUp += () => Debug.Log("[Game] Time up!");
        _mp.Game.OnGameEnd += OnGameEnd;
        _mp.Game.OnGameRestart += OnGameRestart;
        _mp.Game.OnErrorNotify += (json) => Debug.LogError($"[Game] Error: {json}");
        _mp.Game.OnBotLeave += () => Debug.Log("[Game] Server bot left");

        // 3. Enable game module and call Init.
        var options = new MultiplayerInitOptions
        {
            modules = new ModulesConfig
            {
                game = new ModuleOption
                {
                    enabled = true,
                    play_time = 60,
                    total_player = 4,
                    min_total_player = 2,
                    max_total_player = 4
                }
            }
        };
        await _mp.Init(options);

        // 4. Signal that this player is ready to start.
        _mp.Game.Ready();
    }

    private void OnMasterNotify(string json)
    {
        _isMaster = _mp.IsMasterUser();
        Debug.Log($"[Game] Master assigned. Am I master? {_isMaster}");
    }

    private void OnAllReady(string json)
    {
        Debug.Log("[Game] All players ready");
        // Only the master kicks off the game start.
        if (_isMaster)
            _mp.Game.TriggerGameStart();
    }

    private void OnGameEnd()
    {
        Debug.Log("[Game] Game ended — show results");
        ShowEndScreen();
        // Only master restarts.
        if (_isMaster)
            _mp.Game.TriggerGameRestart();
    }

    private void OnGameRestart()
    {
        Debug.Log("[Game] Restarting — reset local state");
        ResetGameState();
        // Server sends bot_ready to all again; Ready() is auto-called by GameModule.
    }

    private void ShowCountdown(string label, string json) { /* update UI */ }
    private void UpdateStatus(string msg) { /* update status label */ }
    private void ShowEndScreen() { /* show results screen */ }
    private void ResetGameState() { /* clear score, timers, etc. */ }
}
```

## Mandatory Compliance Gates (MUST PASS)

1. **MUST** complete `viverse-unity-multiplayer` bootstrap before accessing `mp.Game`.
2. **MUST** subscribe to `OnMasterNotify`, `OnPlayerAllReady`, `OnCountdownToStart` before calling `Ready()`.
3. **MUST** enable the game module: `modules.game = new ModuleOption { enabled = true }` in `MultiplayerInitOptions`.
4. **MUST** call `mp.Game.Ready()` after `Init` completes to enter the ready pool.
5. **MUST** guard `TriggerGameStart`, `TriggerGameEnd`, `TriggerGameRestart` with `IsMasterUser()` — only the master sends these.
6. **MUST NOT** add a C# state machine for game phases; the server drives lifecycle transitions.
7. **MUST** handle `OnGameEnd` or `OnGameTimeUp` to clean up or transition the game.
8. **MUST** handle `OnGameRestart` to reset local state before the next round.
9. **MUST** use message format `{source:"client", messageType:"bot", type:"game", event:"<event>"}` for any custom game messages.
10. **MUST NOT** add WebSocket, WebRTC, or transport logic in C#. `GameModule` is a thin passthrough.
11. **MUST** handle `OnErrorNotify` to surface server errors rather than silently ignoring them.
12. **MUST** handle `OnMasterNotify` and update local `_isMaster` to match `mp.IsMasterUser()`.

## Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| `OnPlayerAllReady` fires but game never starts | Call `TriggerGameStart()` from `OnPlayerAllReady` handler, guarded by `IsMasterUser()` |
| `Ready()` called before `Init()` | Wait for `Init()` to complete before calling `Ready()` |
| Game module events never fire | Confirm `modules.game.enabled = true` was passed to `Init()` |
| Game restarts but events don't fire again | After `OnGameRestart`, the server sends `bot_ready` again. `GameModule` auto-calls `Ready()` when it detects the local `PeerId` in the `user_ids` list |
| Two master users | `OnMasterNotify` carries the authoritative `master_user` field — always derive master status from it via `IsMasterUser()`, not a local flag |
| `OnCountdownToStart` data is null | Parse with `MiniJson.Deserialize(json)` not `JsonUtility` — countdown payload is a nested object |
| `TriggerGameEnd` called by non-master | Server may reject or ignore it. Always gate on `IsMasterUser()` |
| `OnGameEnd` not fired | The server also fires `OnGameTimeUp` when time expires. Handle both |

## Cross-Module Comparison: Game Module vs Client-Side State Machine

| Aspect | Game Module (`mp.Game`) | Custom C# State Machine (anti-pattern) |
|--------|-------------------------|----------------------------------------|
| Source of truth | Server bot (authoritative) | Master client (contested across peers) |
| Master election | Handled server-side via `OnMasterNotify` | Manual, race-prone on join/leave |
| Countdown timing | Server-driven; all peers receive identical `OnCountdownToStart` | Each client counts locally; drift accumulates |
| Ready handshake | `Ready()` -> server aggregates -> `OnPlayerAllReady` | Client must broadcast, dedupe, and count peer readies |
| Player-count validation | Server enforces `min_total_player` / `max_total_player` | Client must recount on every join/leave |
| Late-join safety | Server replays state to new joiners via `bot_ready` | Late joiners see stale local state |
| Restart flow | `TriggerGameRestart()` -> server broadcasts `game_restart` | Master must broadcast + every client resets + master hand-off logic |
| Method | `Ready()`, `TriggerGameStart()`, `TriggerGameEnd()`, `TriggerGameRestart()` | Ad-hoc RPCs over General module or custom messaging |
| Event | `OnPlayerAllReady`, `OnCountdownToStart`, `OnGameEnd`, `OnGameRestart` | Custom event bus, manually implemented |

Use `mp.Game` for any multiplayer session that has a bounded match with ready-up, countdown, and end conditions. Only bypass the module when your game is completely session-less (e.g., a persistent social world). Compliance Gate #6 forbids C# state machines for lifecycle phases — the Game Module IS the state machine, delivered as a service.

The Game Module also complements the data-carrying modules: `mp.Game` governs the lifecycle (when a match starts/ends), while `mp.NetworkSync`, `mp.ActionSync`, and `mp.Leaderboard` carry the in-match data. A typical match uses all four together.
