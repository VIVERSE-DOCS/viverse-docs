---
name: viverse-unity-action-sync
description: Unity Play SDK ActionSync Module — broadcasts discrete competitive game actions (attacks, abilities, emotes) to all peers with caller-supplied action IDs for de-duplication.
prerequisites: [Unity 2021.2+, App ID from VIVERSE Studio, MultiplayerClient initialized via viverse-unity-multiplayer skill]
tags: [unity, multiplayer, play-sdk, module, webgl, editor, action-sync, competitive, dedup]
---

# Play Unity SDK — Action Sync Module

Broadcast discrete game actions (attacks, abilities, emotes, skill triggers) to all peers. Each action carries a caller-supplied `action_id` for de-duplication on receive.

## When To Use This Skill

Use when a Unity project needs:
- Broadcasting a player attack, ability, or skill use to all other players
- Emote or animation trigger synchronization
- Any discrete, one-shot event that should fire exactly once on every client
- Competitive combat: hit validation, skill activation, dodge notifications

## Architecture Overview

ActionSync is optimized for discrete, event-driven messages. Unlike NetworkSync's per-frame stream, ActionSync fires only when an action occurs:

- **WebGL**: C# calls jslib -> jslib sends `competition` event over the TypeScript SDK's data channel. Inbound events arrive as `actionsync/onCompetition` via `SendMessage` -> `DispatchEvent`.
- **Editor**: Proxy frame with `type:"action_sync"`, `event:"competition"` -> `HandleProxyMessage` -> `OnModuleMessage` -> `ActionSyncModule.HandleModuleMessage`.

Both paths filter self-originated messages before firing `OnCompetition`. You still need de-duplication by `action_id` in case the broadcast reaches a peer via multiple paths.

## Dependency

Requires both `viverse-unity-matchmaking` and `viverse-unity-multiplayer` skills. `MultiplayerClient.Initialize` + `Init` with `modules.actionSync.enabled = true` must be complete before calling `Competition`.

## Read Order

1. This file (API + compliance gates)
2. [patterns/action-sync-pattern.md](patterns/action-sync-pattern.md)

## Prerequisites

1. Unity 2021.2+ with .NET 4.x runtime
2. App ID from [VIVERSE Studio](https://studio.viverse.com/)
3. Active room from `MatchmakingClient`
4. `MultiplayerClient.Initialize` + `Init` completed with `modules.actionSync.enabled = true`

## Module Access

```csharp
// After Initialize + Init complete
var mp = MultiplayerClient.Instance;
ActionSyncModule actionSync = mp.ActionSync;

// Enable in Init options
var options = new MultiplayerInitOptions
{
    modules = new ModulesConfig
    {
        actionSync = new ModuleOption { enabled = true }
    }
};
await mp.Init(options);
```

## API Reference

### Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `Competition(string actionName, string actionMsg, string actionId)` | `void` | Broadcast a discrete action to all peers. `actionName` is a short label (e.g., `"attack"`). `actionMsg` is an arbitrary JSON-safe string carrying action details. `actionId` is a caller-supplied unique identifier for de-duplication. |

### Events

| Event | Signature | When |
|-------|-----------|------|
| `OnCompetition` | `Action<string>` | Received a competition action from another peer. JSON contains `user_id`, `action_name`, `action_msg`, `action_id`, `timestamp`. Self-messages are already filtered. |

## Message Protocol

### Outbound (client to server/peers)

```json
{
  "source": "client",
  "messageType": "bot",
  "type": "action_sync",
  "event": "competition",
  "user_id": "<PeerId>",
  "action_name": "attack",
  "action_msg": "{\"target\":\"enemy_01\",\"damage\":25}",
  "action_id": "a3f9c1d2",
  "timestamp": 1700000000000
}
```

### Inbound (server/peers to client)

```json
{
  "source": "bot",
  "type": "action_sync",
  "event": "competition",
  "user_id": "<sender-PeerId>",
  "action_name": "attack",
  "action_msg": "{\"target\":\"enemy_01\",\"damage\":25}",
  "action_id": "a3f9c1d2",
  "timestamp": 1700000000000
}
```

## Typical Integration Flow

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using ViverseSDK;

public class CombatController : MonoBehaviour
{
    private MultiplayerClient _mp;

    // De-dup set: track action_ids we've already processed.
    private HashSet<string> _processedActions = new HashSet<string>();

    public void Initialize(MultiplayerClient mp)
    {
        _mp = mp;

        // Subscribe BEFORE any Competition() calls.
        _mp.ActionSync.OnCompetition += HandleCompetition;
    }

    // Called by player input (button press, key down, etc.)
    public void Attack(string targetId, int damage)
    {
        // Fresh unique ID per action — 8 hex chars is sufficient.
        string actionId = Guid.NewGuid().ToString().Substring(0, 8);
        string actionMsg = $"{{\"target\":\"{targetId}\",\"damage\":{damage}}}";

        _mp.ActionSync.Competition("attack", actionMsg, actionId);
        Debug.Log($"[ActionSync] Sent attack: target={targetId} dmg={damage} id={actionId}");
    }

    public void TriggerAbility(string abilityName)
    {
        string actionId = Guid.NewGuid().ToString().Substring(0, 8);
        _mp.ActionSync.Competition("ability", abilityName, actionId);
    }

    // Receive remote actions
    private void HandleCompetition(string json)
    {
        var data = MiniJson.Deserialize(json) as Dictionary<string, object>;
        if (data == null) return;

        string userId    = data.ContainsKey("user_id")     ? data["user_id"].ToString()     : "";
        string actionName = data.ContainsKey("action_name") ? data["action_name"].ToString() : "";
        string actionMsg  = data.ContainsKey("action_msg")  ? data["action_msg"].ToString()  : "";
        string actionId   = data.ContainsKey("action_id")   ? data["action_id"].ToString()   : "";

        // De-dup: skip if we've already processed this action.
        if (_processedActions.Contains(actionId))
        {
            Debug.Log($"[ActionSync] Skipping duplicate action: {actionId}");
            return;
        }
        _processedActions.Add(actionId);

        // Route by action name.
        switch (actionName)
        {
            case "attack":
                HandleRemoteAttack(userId, actionMsg);
                break;
            case "ability":
                HandleRemoteAbility(userId, actionMsg);
                break;
            default:
                Debug.Log($"[ActionSync] Unknown action from {userId}: {actionName}");
                break;
        }
    }

    private void HandleRemoteAttack(string userId, string actionMsg)
    {
        var payload = MiniJson.Deserialize(actionMsg) as Dictionary<string, object>;
        if (payload == null) return;
        string target = payload.ContainsKey("target") ? payload["target"].ToString() : "";
        int damage = payload.ContainsKey("damage") ? Convert.ToInt32(payload["damage"]) : 0;
        Debug.Log($"[ActionSync] {userId} attacked {target} for {damage} damage");
        // Apply hit effect, reduce HP, etc.
    }

    private void HandleRemoteAbility(string userId, string actionMsg)
    {
        Debug.Log($"[ActionSync] {userId} used ability: {actionMsg}");
    }

    // Clean up de-dup set periodically (optional, prevents unbounded growth)
    private void OnGameRestart()
    {
        _processedActions.Clear();
    }
}
```

## Mandatory Compliance Gates (MUST PASS)

1. **MUST** complete `viverse-unity-multiplayer` bootstrap before accessing `mp.ActionSync`.
2. **MUST** enable `modules.actionSync = new ModuleOption { enabled = true }` in `MultiplayerInitOptions`.
3. **MUST** subscribe to `OnCompetition` before calling `Competition()`.
4. **MUST** supply a unique `actionId` per `Competition()` call (e.g., `Guid.NewGuid().ToString().Substring(0, 8)`).
5. **MUST** de-duplicate received actions by `action_id` using a `HashSet<string>`.
6. **MUST** use `{source:"client", messageType:"bot", type:"action_sync", event:"competition"}` message format.
7. **MUST NOT** add WebSocket or WebRTC logic in C#. `ActionSyncModule` is a thin passthrough.
8. **MUST NOT** encode authoritative game state (current HP, positions) in `action_msg`. ActionSync announces events; it doesn't carry canonical state.
9. **MUST** ensure `action_msg` is a JSON-safe string. Avoid unescaped double-quotes inside the value.
10. **SHOULD** clear the `_processedActions` set on game restart to prevent unbounded growth.
11. **MUST NOT** throttle `Competition()` the way you would `UpdateMyPosition`. Each action should fire once, not on a timer.
12. **SHOULD** route received actions via a `switch(actionName)` dispatcher for extensibility.

## Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Same action applied twice | Add `HashSet<string>` de-dup by `action_id` |
| `action_msg` parse fails | Ensure `action_msg` is a valid JSON string, not a raw C# object dump |
| Missing `action_id` | Always generate fresh ID per call; never reuse or leave empty |
| Module events never fire | Confirm `actionSync.enabled = true` in `Init()` options |
| Module type mismatch in message | Use `"action_sync"` (underscore), not `"actionsync"` |
| Sending repeated actions from Update | Gate `Competition()` on user input or one-shot trigger, not `Update()` |
| De-dup set grows without bound | Clear it in `OnGameRestart` or after each match |

## Cross-Module Comparison: ActionSync vs NetworkSync

| Aspect | ActionSync | NetworkSync |
|--------|-----------|------------|
| Update frequency | Discrete, on-event | Continuous, per-frame |
| Data volume | Low (rare bursts) | High (many small packets) |
| Typical use | Attacks, abilities, emotes | Position, rotation, state |
| De-duplication | Required (action_id) | Not needed (latest wins) |
| Self-filtering | Built-in | Built-in |
| Method | `Competition(name, msg, id)` | `UpdateMyPosition(json)` |
| Event | `OnCompetition` | `OnNotifyPositionUpdate` |
