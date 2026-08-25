# Game Module Pattern — Full Lifecycle

## Overview

This pattern wires the complete game lifecycle: waiting for players, master assignment,
all-ready detection, countdown, active play, time-up/end, and restart. It's the canonical
flow for any competitive multiplayer session using `GameModule`.

## State Flow

```
Join room (MatchmakingClient)
         |
         v
Initialize + Init (MultiplayerClient)
         |
         v
Game.Ready() ─────────────────────────────────────────────────────────────────────────────┐
         |                                                                                  |
         v                                                                                  |
OnWaitForPlayer     <── not all players joined yet                                         |
         |                                                                                  |
         v (when min_total_player reached)                                                 |
OnPlayerAllReady                                                                            |
         |                                                                                  |
         v (master calls TriggerGameStart)                                                  |
OnCountdownToStart  <── fires once per second until ready_time reaches 0                   |
         |                                                                                  |
         v                                                                                  |
[Active game]                                                                               |
         |                                                                                  |
         v (play_time elapsed)                                                              |
OnCountdownToEnd    <── fires once per second during end countdown                         |
         |                                                                                  |
         v                                                                                  |
OnGameTimeUp / OnGameEnd                                                                    |
         |                                                                                  |
         v (master calls TriggerGameRestart)                                                |
OnGameRestart ──────────────────────────────────────────────────────────────────────────────┘
```

## Full Implementation

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using ViverseSDK;

/// <summary>
/// Full game lifecycle using GameModule.
/// Assumes MultiplayerClient.Initialize + Init have already been called
/// (handled by the viverse-unity-multiplayer skill bootstrap).
/// </summary>
public class GameLifecycleController : MonoBehaviour
{
    // ─── State ───────────────────────────────────────────────────────────────────

    private MultiplayerClient _mp;
    private bool _isMaster;

    // Track current phase for UI / other systems
    public enum Phase { Lobby, Countdown, Playing, Ended }
    public Phase CurrentPhase { get; private set; } = Phase.Lobby;

    // ─── Wiring (call after Init completes) ──────────────────────────────────────

    public void WireGameModule(MultiplayerClient mp)
    {
        _mp = mp;

        // Subscribe ALL events before calling Ready().
        _mp.Game.OnBotLeave          += () => Debug.Log("[Game] Server bot left");
        _mp.Game.OnMasterNotify      += HandleMasterNotify;
        _mp.Game.OnWaitForPlayer     += HandleWaitForPlayer;
        _mp.Game.OnPlayerAllReady    += HandleAllReady;
        _mp.Game.OnPlayerOverLimit   += () => Debug.LogWarning("[Game] Room is over player limit");
        _mp.Game.OnCountdownToStart  += HandleCountdownToStart;
        _mp.Game.OnCountdownToEnd    += HandleCountdownToEnd;
        _mp.Game.OnGameTimeUp        += HandleTimeUp;
        _mp.Game.OnGameEnd           += HandleGameEnd;
        _mp.Game.OnGameRestart       += HandleGameRestart;
        _mp.Game.OnErrorNotify       += (json) => Debug.LogError($"[Game] Server error: {json}");

        // Signal readiness.
        _mp.Game.Ready();
        Debug.Log("[Game] Ready sent");
    }

    // ─── Handlers ─────────────────────────────────────────────────────────────────

    private void HandleMasterNotify(string json)
    {
        // Derive master status from the SDK, not from the payload directly.
        _isMaster = _mp.IsMasterUser();
        Debug.Log($"[Game] Master notify — am I master? {_isMaster}");

        if (_isMaster)
            Debug.Log("[Game] I am the master client");
    }

    private void HandleWaitForPlayer(string json)
    {
        // Parse if you need current player count.
        var data = MiniJson.Deserialize(json) as Dictionary<string, object>;
        Debug.Log($"[Game] Waiting for players: {json}");
        CurrentPhase = Phase.Lobby;
        // Update UI: show waiting room
    }

    private void HandleAllReady(string json)
    {
        Debug.Log("[Game] All players ready");
        CurrentPhase = Phase.Lobby;
        // Only master triggers the start — avoids duplicate starts.
        if (_isMaster)
        {
            Debug.Log("[Game] I'm master — triggering game start");
            _mp.Game.TriggerGameStart();
        }
    }

    private void HandleCountdownToStart(string json)
    {
        var data = MiniJson.Deserialize(json) as Dictionary<string, object>;
        // 'countdown' key typically contains remaining seconds
        string countdownVal = data != null && data.ContainsKey("countdown")
            ? data["countdown"].ToString()
            : "?";
        Debug.Log($"[Game] Game starts in {countdownVal}s");
        CurrentPhase = Phase.Countdown;
        // Update countdown UI
    }

    private void HandleCountdownToEnd(string json)
    {
        var data = MiniJson.Deserialize(json) as Dictionary<string, object>;
        string countdownVal = data != null && data.ContainsKey("countdown")
            ? data["countdown"].ToString()
            : "?";
        Debug.Log($"[Game] Game ends in {countdownVal}s");
        // Update end-countdown UI
    }

    private void HandleTimeUp()
    {
        Debug.Log("[Game] Time is up!");
        CurrentPhase = Phase.Ended;
        OnGameOver();
    }

    private void HandleGameEnd()
    {
        Debug.Log("[Game] Game ended");
        CurrentPhase = Phase.Ended;
        OnGameOver();
    }

    private void HandleGameRestart()
    {
        Debug.Log("[Game] Game restarting — resetting local state");
        CurrentPhase = Phase.Lobby;
        ResetGameState();
        // GameModule auto-calls Ready() when bot_ready arrives after restart.
        // No need to call _mp.Game.Ready() here manually.
    }

    // ─── Helpers ──────────────────────────────────────────────────────────────────

    private void OnGameOver()
    {
        // Show results screen, submit final scores to LeaderboardClient, etc.
        // Master may call TriggerGameRestart() after a delay.
        if (_isMaster)
            Invoke(nameof(RestartAfterDelay), 5f);
    }

    private void RestartAfterDelay()
    {
        _mp.Game.TriggerGameRestart();
    }

    private void ResetGameState()
    {
        // Clear scores, respawn players, reset timers.
        // Called on ALL clients when OnGameRestart fires.
    }
}
```

## ModuleOption Fields for Game Module

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `enabled` | `bool` | `false` | Must be `true` to activate the module |
| `play_time` | `int` | `30` | Seconds the game runs before `OnGameTimeUp` fires |
| `total_player` | `int` | `4` | Expected player count |
| `min_total_player` | `int` | `2` | Minimum players before ready phase starts |
| `max_total_player` | `int` | `4` | Players above this trigger `OnPlayerOverLimit` |
| `ready_time` | `int` | `3` | Countdown seconds before game starts |
| `start_delay_time` | `float` | `0.5` | Extra delay after countdown completes |
| `change_second` | `int` | `10` | Seconds for game-update / change phase |
| `wait_player_timeout` | `int` | `100` | Seconds to wait for min players before timing out |

## Notes

- `OnGameRestart` fires on ALL clients. Reset score, UI, and player state in the handler.
- `OnGameEnd` and `OnGameTimeUp` can both fire for the same session. Handle both or use a guard flag.
- `TriggerGameUpdate(int totalPlayer)` lets the master update the expected player count mid-session.
- The server bot auto-calls `Ready()` for each client when `bot_ready` arrives. `GameModule`
  checks if the local `PeerId` is in the `user_ids` list and sends `client_ready` automatically,
  so you won't miss the ready cycle after a restart.
