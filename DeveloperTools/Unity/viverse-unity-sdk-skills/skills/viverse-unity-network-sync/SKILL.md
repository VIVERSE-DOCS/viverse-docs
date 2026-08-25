---
name: viverse-unity-network-sync
description: Unity Play SDK NetworkSync Module — continuous per-frame position and transform synchronization for players and game entities via the NetworkSyncModule property on MultiplayerClient.
prerequisites: [Unity 2021.2+, App ID from VIVERSE Studio, MultiplayerClient initialized via viverse-unity-multiplayer skill]
tags: [unity, multiplayer, play-sdk, module, webgl, editor, network-sync, transform, position]
---

# Play Unity SDK — Network Sync Module

Broadcast and receive continuous per-frame transforms for players and game entities over the WebRTC data channel.

## When To Use This Skill

Use when a Unity project needs:
- Smooth real-time position and rotation of remote players
- Entity (prop, projectile, vehicle) position sync driven by a specific peer
- Per-frame or fixed-rate transform broadcasting at 10-30 Hz
- Distinction between player-owned positions and object-owned positions (by entity ID)

## Architecture Overview

NetworkSync is built for high-frequency, low-latency updates:

- **WebGL**: C# calls jslib `UpdateMyPosition` -> jslib forwards via TypeScript SDK's data channel. Inbound events arrive as `networksync/onNotifyPositionUpdate` via `SendMessage` -> `DispatchEvent`.
- **Editor**: NativeWebSocket proxy delivers frames with `type:"network_sync"` -> `HandleProxyMessage` -> `OnModuleMessage` -> `NetworkSyncModule.HandleModuleMessage`.

Both paths filter out self-originated messages before firing `OnNotifyPositionUpdate`.

The module adds a Unix-millisecond `timestamp` to every outbound packet automatically.

## Dependency

Requires both `viverse-unity-matchmaking` and `viverse-unity-multiplayer` skills. `MultiplayerClient.Initialize` + `Init` with `modules.networkSync.enabled = true` must be complete before calling `UpdateMyPosition` or `UpdateEntityPosition`.

## Read Order

1. This file (API + compliance gates)
2. [patterns/network-sync-pattern.md](patterns/network-sync-pattern.md)

## Prerequisites

1. Unity 2021.2+ with .NET 4.x runtime
2. App ID from [VIVERSE Studio](https://studio.viverse.com/)
3. Active room from `MatchmakingClient`
4. `MultiplayerClient.Initialize` + `Init` completed with `modules.networkSync.enabled = true`

## Module Access

```csharp
// After Initialize + Init complete
var mp = MultiplayerClient.Instance;
NetworkSyncModule sync = mp.NetworkSync;

// Enable in Init options
var options = new MultiplayerInitOptions
{
    modules = new ModulesConfig
    {
        networkSync = new ModuleOption { enabled = true }
    }
};
await mp.Init(options);
```

## API Reference

### Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `UpdateMyPosition(string dataJson)` | `void` | Broadcast local player position. `dataJson` is any JSON object (e.g., `{"x":1.0,"y":0,"z":2.0,"ry":90.0}`). The module appends `user_id` and `timestamp` automatically. |
| `UpdateEntityPosition(string entityId, string dataJson)` | `void` | Broadcast position of a game entity this client owns (e.g., a projectile or vehicle). Adds `entity_id` field so receivers can distinguish it from player updates. |

### Events

| Event | Signature | When |
|-------|-----------|------|
| `OnNotifyPositionUpdate` | `Action<string>` | Position update received from another peer. JSON contains `user_id`, `data`, `timestamp`, and optionally `entity_id`. Self-messages are already filtered out. |
| `OnNotifyRemove` | `Action<string>` | A peer removed an entity. JSON contains `user_id` and `entity_id`. |

## Message Protocol

### Outbound — player update (client to server/peers)

```json
{
  "source": "client",
  "messageType": "bot",
  "type": "network_sync",
  "event": "notify_position",
  "user_id": "<PeerId>",
  "data": { "x": 1.5, "y": 0.0, "z": -3.2, "ry": 45.0 },
  "timestamp": 1700000000000
}
```

### Outbound — entity update (adds `entity_id`)

```json
{
  "source": "client",
  "messageType": "bot",
  "type": "network_sync",
  "event": "notify_position",
  "user_id": "<PeerId>",
  "entity_id": "projectile_001",
  "data": { "x": 5.0, "y": 1.0, "z": 2.0 },
  "timestamp": 1700000000000
}
```

### Inbound (server/peers to client)

```json
{
  "source": "bot",
  "type": "network_sync",
  "event": "notify_position",
  "user_id": "<sender-PeerId>",
  "data": { "x": 1.5, "y": 0.0, "z": -3.2, "ry": 45.0 },
  "timestamp": 1700000000000
}
```

Entity updates include `"entity_id": "..."`. Distinguish player vs entity updates by checking whether `entity_id` is present.

## Typical Integration Flow

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using ViverseSDK;

public class PlayerSyncController : MonoBehaviour
{
    private MultiplayerClient _mp;

    // Remote player representations keyed by peerId
    private Dictionary<string, RemotePlayer> _remotePlayers = new Dictionary<string, RemotePlayer>();

    // Throttle: send at ~20 Hz
    private float _syncInterval = 1f / 20f;
    private float _syncTimer;

    public void SetupSync(MultiplayerClient mp)
    {
        _mp = mp;

        // Subscribe BEFORE any send calls.
        _mp.NetworkSync.OnNotifyPositionUpdate += OnPositionUpdate;
        _mp.NetworkSync.OnNotifyRemove += OnRemoveEntity;

        // Enable networkSync in Init options (done externally by multiplayer bootstrap).
    }

    // Send local player position at fixed rate
    private void Update()
    {
        if (_mp == null) return;

        _syncTimer -= Time.deltaTime;
        if (_syncTimer > 0) return;
        _syncTimer = _syncInterval;

        var pos = transform.position;
        var rot = transform.eulerAngles;
        var json = $"{{\"x\":{pos.x:F3},\"y\":{pos.y:F3},\"z\":{pos.z:F3}," +
                   $"\"rx\":{rot.x:F3},\"ry\":{rot.y:F3},\"rz\":{rot.z:F3}}}";
        _mp.NetworkSync.UpdateMyPosition(json);
    }

    // Receive remote player position
    private void OnPositionUpdate(string json)
    {
        var data = MiniJson.Deserialize(json) as Dictionary<string, object>;
        if (data == null) return;

        string userId = data.ContainsKey("user_id") ? data["user_id"].ToString() : "";
        bool isEntity = data.ContainsKey("entity_id");

        if (isEntity)
        {
            string entityId = data["entity_id"].ToString();
            var posData = data.ContainsKey("data") ? data["data"] as Dictionary<string, object> : null;
            UpdateEntityTransform(entityId, posData);
        }
        else
        {
            var posData = data.ContainsKey("data") ? data["data"] as Dictionary<string, object> : null;
            UpdateRemotePlayerTransform(userId, posData);
        }
    }

    private void OnRemoveEntity(string json)
    {
        var data = MiniJson.Deserialize(json) as Dictionary<string, object>;
        if (data == null) return;
        string entityId = data.ContainsKey("entity_id") ? data["entity_id"].ToString() : "";
        if (!string.IsNullOrEmpty(entityId))
            DespawnEntity(entityId);
    }

    private void UpdateRemotePlayerTransform(string userId, Dictionary<string, object> pos)
    {
        if (!_remotePlayers.TryGetValue(userId, out var remote)) return;
        // Use Lerp for smooth interpolation between network updates.
        if (pos == null) return;
        float x = pos.ContainsKey("x") ? Convert.ToSingle(pos["x"]) : 0f;
        float y = pos.ContainsKey("y") ? Convert.ToSingle(pos["y"]) : 0f;
        float z = pos.ContainsKey("z") ? Convert.ToSingle(pos["z"]) : 0f;
        remote.SetTargetPosition(new Vector3(x, y, z));
    }

    private void UpdateEntityTransform(string entityId, Dictionary<string, object> pos) { /* similar */ }
    private void DespawnEntity(string entityId) { /* destroy GameObject */ }
}
```

## Mandatory Compliance Gates (MUST PASS)

1. **MUST** complete `viverse-unity-multiplayer` bootstrap before accessing `mp.NetworkSync`.
2. **MUST** enable `modules.networkSync = new ModuleOption { enabled = true }` in `MultiplayerInitOptions`.
3. **MUST** subscribe to `OnNotifyPositionUpdate` before sending any position updates.
4. **MUST NOT** call `UpdateMyPosition` every Unity frame unconditionally. Throttle to 10-30 Hz.
5. **MUST NOT** add WebSocket or WebRTC logic in C#. `NetworkSyncModule` is a thin passthrough.
6. **MUST** use `{source:"client", messageType:"bot", type:"network_sync", event:"notify_position"}` format.
7. **MUST** use `UpdateEntityPosition(entityId, json)` for non-player objects. Reserve `UpdateMyPosition` for the local player's main avatar.
8. **MUST** handle `OnNotifyRemove` to despawn entities when notified.
9. **SHOULD** interpolate received positions (Vector3.Lerp or SmoothDamp) between network frames to prevent jitter.
10. **MUST** check for `entity_id` field in `OnNotifyPositionUpdate` data to distinguish entity updates from player updates.
11. **MUST NOT** re-implement self-filtering in the event handler. `NetworkSyncModule` already drops self-originated messages before firing the event.
12. **SHOULD** include `timestamp` awareness for dead-reckoning and latency compensation in competitive games.

## Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Remote players jitter | Apply Vector3.Lerp / SmoothDamp with a target position, not direct assignment |
| Bandwidth flooded | Throttle to 20 Hz; 60 fps sends 3x more data than needed |
| Can't tell player vs entity updates | Check `data.ContainsKey("entity_id")` in `OnNotifyPositionUpdate` handler |
| Own position updates received | Module already filters these. If you see them, verify `PeerId` is set correctly |
| Module events never fire | Confirm `networkSync.enabled = true` passed to `Init()` |
| Float precision issues | Use `:F3` format specifier when serializing floats, or round to 3 decimal places |
| Module type mismatch | The module type string is `"network_sync"` (with underscore), not `"networksync"` |

## Cross-Module Comparison: NetworkSync vs ActionSync

| Aspect | NetworkSync | ActionSync |
|--------|------------|-----------|
| Update frequency | Continuous, per-frame (10-30 Hz) | Discrete, event-driven |
| Data volume | High (many small packets) | Low (rare bursts) |
| Typical use | Position, rotation, animation state | Attacks, abilities, emotes |
| De-duplication | Not needed (latest wins) | Required (action_id per event) |
| Self-filtering | Built-in | Built-in |
| Method | `UpdateMyPosition(json)` | `Competition(name, msg, id)` |
| Event | `OnNotifyPositionUpdate` | `OnCompetition` |
