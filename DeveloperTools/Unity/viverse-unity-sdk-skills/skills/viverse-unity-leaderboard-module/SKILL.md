---
name: viverse-unity-leaderboard-module
description: Unity Play SDK Leaderboard Module — broadcasts real-time in-room scores to all peers via WebRTC data channel during an active multiplayer session (ephemeral, not persisted to backend).
prerequisites: [Unity 2021.2+, App ID from VIVERSE Studio, MultiplayerClient initialized via viverse-unity-multiplayer skill]
tags: [unity, multiplayer, play-sdk, module, webgl, editor, leaderboard, in-room, realtime, score]
---

# Play Unity SDK — Leaderboard Module

Broadcast score updates to all peers in real time during a multiplayer room session. Scores are transmitted over the WebRTC data channel and are ephemeral — they exist only for the lifetime of the room. For persistent global rankings, use `LeaderboardClient` (see viverse-unity-leaderboard skill).

## When To Use This Skill

Use when a Unity project needs:
- Live in-room scoreboards that update as players earn points
- Real-time competitive ranking within a single multiplayer session
- Score announcements during gameplay (kill streaks, combo counts, round totals)
- Per-player score display visible to all room participants without a server-side record

## Architecture Overview

LeaderboardModule is one of five sub-modules of `MultiplayerClient`. It shares the same WebRTC data channel as the other modules and is routed by `type:"leaderboard"` in the message envelope.

- **WebGL**: C# calls jslib -> jslib sends `update_record` event via the TypeScript SDK's data channel. Inbound events arrive as `leaderboard/onLeaderboardUpdate` via `SendMessage` -> `DispatchEvent`.
- **Editor**: Proxy frame with `type:"leaderboard"`, `event:"update_record"` -> `HandleProxyMessage` -> `OnModuleMessage` -> `LeaderboardModule.HandleModuleMessage`.

Both paths fire `OnLeaderboardUpdate` for every score update received from another peer. Self-originated messages are not reflected back.

## Dependency

Requires both `viverse-unity-matchmaking` and `viverse-unity-multiplayer` skills. `MultiplayerClient.Initialize` + `Init` with `modules.leaderboard.enabled = true` must be complete before calling `LeaderboardUpdate`.

## Read Order

1. This file (API + compliance gates)
2. [patterns/leaderboard-module-pattern.md](patterns/leaderboard-module-pattern.md)

## Prerequisites

1. Unity 2021.2+ with .NET 4.x runtime
2. App ID from [VIVERSE Studio](https://studio.viverse.com/)
3. Active room from `MatchmakingClient`
4. `MultiplayerClient.Initialize` + `Init` completed with `modules.leaderboard.enabled = true`

## Module Access

```csharp
// After Initialize + Init complete
var mp = MultiplayerClient.Instance;
LeaderboardModule leaderboard = mp.Leaderboard;

// Enable in Init options
var options = new MultiplayerInitOptions
{
    modules = new ModulesConfig
    {
        leaderboard = new ModuleOption { enabled = true }
    }
};
await mp.Init(options);
```

## API Reference

### Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `LeaderboardUpdate(int score)` | `void` | Broadcast the local player's current score to all peers in the room. Transmits score as an integer. |

### Events

| Event | Signature | When |
|-------|-----------|------|
| `OnLeaderboardUpdate` | `Action<string>` | Received a score update from another peer. JSON contains `user_id`, `score`, `type`, `event`, and `source` fields. |

## Message Protocol

### Outbound (client to server/peers)

```json
{
  "source": "client",
  "messageType": "bot",
  "type": "leaderboard",
  "event": "update_record",
  "user_id": "<PeerId>",
  "score": 150
}
```

### Inbound (server/peers to client)

```json
{
  "source": "bot",
  "type": "leaderboard",
  "event": "update_record",
  "user_id": "<sender-PeerId>",
  "score": 150
}
```

## Typical Integration Flow

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using ViverseSDK;

public class ScoreboardController : MonoBehaviour
{
    private MultiplayerClient _mp;

    // Local scoreboard: maps PeerId -> score
    private Dictionary<string, int> _scoreboard = new Dictionary<string, int>();

    public void Initialize(MultiplayerClient mp)
    {
        _mp = mp;

        // Subscribe BEFORE any LeaderboardUpdate() calls.
        _mp.Leaderboard.OnLeaderboardUpdate += HandleLeaderboardUpdate;

        // Track own PeerId in the scoreboard.
        _scoreboard[_mp.PeerId] = 0;
    }

    // Called when the local player's score changes (e.g., on kill, on combo).
    public void ReportScore(int newScore)
    {
        _scoreboard[_mp.PeerId] = newScore;
        _mp.Leaderboard.LeaderboardUpdate(newScore);
        Debug.Log($"[Leaderboard] Reported score: {newScore}");
    }

    // Receive remote score updates.
    private void HandleLeaderboardUpdate(string json)
    {
        var data = MiniJson.Deserialize(json) as Dictionary<string, object>;
        if (data == null) return;

        string userId = data.ContainsKey("user_id") ? data["user_id"].ToString() : "";
        int score = data.ContainsKey("score") ? Convert.ToInt32(data["score"]) : 0;

        if (string.IsNullOrEmpty(userId)) return;

        _scoreboard[userId] = score;
        Debug.Log($"[Leaderboard] {userId} updated score to {score}");

        RefreshScoreboardUI();
    }

    private void RefreshScoreboardUI()
    {
        // Sort by score descending and update UI labels.
        var sorted = new List<KeyValuePair<string, int>>(_scoreboard);
        sorted.Sort((a, b) => b.Value.CompareTo(a.Value));

        for (int i = 0; i < sorted.Count; i++)
        {
            Debug.Log($"  Rank {i + 1}: {sorted[i].Key} - {sorted[i].Value}");
        }
    }

    // Reset scoreboard at match start.
    public void OnMatchStart()
    {
        _scoreboard.Clear();
        _scoreboard[_mp.PeerId] = 0;
    }
}
```

## Mandatory Compliance Gates (MUST PASS)

1. **MUST** complete `viverse-unity-multiplayer` bootstrap before accessing `mp.Leaderboard`.
2. **MUST** enable `modules.leaderboard = new ModuleOption { enabled = true }` in `MultiplayerInitOptions`.
3. **MUST** subscribe to `OnLeaderboardUpdate` before calling `LeaderboardUpdate()` to avoid missing early score events.
4. **MUST** pass `int score` to `LeaderboardUpdate()`. Do not cast floats or strings.
5. **MUST NOT** call `LeaderboardUpdate()` every frame — throttle to score-change events or a timed interval.
6. **MUST NOT** confuse `LeaderboardModule.LeaderboardUpdate()` with `LeaderboardClient.SubmitScore()`. They serve different purposes and write to different backends.
7. **MUST NOT** add WebSocket or WebRTC logic in C#. `LeaderboardModule` is a thin passthrough over `MultiplayerClient`.
8. **MUST NOT** assume scores persist after the room closes. For persistent global rankings, call `LeaderboardClient.SubmitScore()` separately.
9. **SHOULD** maintain a local `Dictionary<string, int>` scoreboard updated from both `LeaderboardUpdate()` (self) and `OnLeaderboardUpdate` (peers).
10. **SHOULD** reset the local scoreboard in `OnMatchStart` or when a new room session begins.

## Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Score never appears for other players | Confirm `leaderboard.enabled = true` in `Init()` options |
| Score floods the data channel | Gate `LeaderboardUpdate()` on score-change events, not `Update()` |
| Own score not reflected locally | Update local dict for self and call `LeaderboardUpdate()` for peers — self messages are not echoed back |
| Score type mismatch | `LeaderboardUpdate(int)` only — do not pass `float` or stringified numbers |
| Scores disappear after match ends | Leaderboard module is ephemeral; persist to `LeaderboardClient` if needed |
| Module type string wrong | Use `"leaderboard"` (no underscore), not `"leaderboard_module"` |

## Cross-Module Comparison: Leaderboard Module vs LeaderboardClient

| Aspect | Leaderboard Module (`mp.Leaderboard`) | LeaderboardClient (viverse-unity-leaderboard) |
|--------|--------------------------------------|----------------------------------------------|
| Transport | WebRTC data channel (via MultiplayerClient) | HTTPS REST (viveport.com API) |
| Persistence | Ephemeral — room lifetime only | Persistent — stored in VIVERSE backend |
| Encryption | None (data channel is already secured) | RSA + AES score encryption |
| Authentication | Inherited from MultiplayerClient session | Requires AccessToken from AuthManager |
| Scope | All peers in the current room | Global leaderboard across all players |
| Query support | No — scores only flow outbound | Yes — GetLeaderboard, GetGuestLeaderboard |
| Typical use | Live in-room scoreboard during a match | End-of-match score submission, global rankings |
| Method | `LeaderboardUpdate(int score)` | `SubmitScore(metaName, value, token)` |
| Event | `OnLeaderboardUpdate` | No event (async Task result) |

Use both together for competitive games: module for live per-round scoring, `LeaderboardClient` for submitting the final result to the global board.
