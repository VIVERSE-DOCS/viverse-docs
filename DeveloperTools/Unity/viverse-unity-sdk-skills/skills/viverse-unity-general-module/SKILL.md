---
name: viverse-unity-general-module
description: Unity Play SDK General Module — sends and receives arbitrary freeform messages between peers over the WebRTC data channel. Also fires connection/disconnection events for all room participants.
prerequisites: [Unity 2021.2+, App ID from VIVERSE Studio, MultiplayerClient initialized via viverse-unity-multiplayer skill]
tags: [unity, multiplayer, play-sdk, module, webgl, editor, general, chat, messaging, freeform]
---

# Play Unity SDK — General Module

Send and receive arbitrary JSON payloads between all peers in a multiplayer room. The General module is the catch-all channel for game logic that does not fit the typed modules (NetworkSync, ActionSync, Leaderboard, Game). It also delivers peer connection and disconnection notifications.

## When To Use This Skill

Use when a Unity project needs:
- In-room text chat between players
- Custom game events not covered by the typed modules (e.g., item pickups, custom state packets)
- Tracking which peers are currently connected or have disconnected
- Broadcasting arbitrary structured data (JSON objects) to all room participants

## Architecture Overview

GeneralModule shares the same WebRTC data channel as the other multiplayer modules. Unlike the typed modules, it has no corresponding field in `ModulesConfig` — it is always instantiated and active once `MultiplayerClient.Init()` completes.

- **WebGL**: C# calls jslib -> jslib sends `general` event via the TypeScript SDK's data channel. Inbound events arrive via `SendMessage` -> `DispatchEvent`.
- **Editor**: Proxy frames with `type:"general"` or `type:"game"` (for `client_connected` / `client_disconnected`) -> `HandleProxyMessage` -> `OnModuleMessage` -> `GeneralModule.HandleModuleMessage`.

`OnMessage` fires for peers only — self-originated messages are filtered before the event fires.

**Critical difference from other modules**: The outbound envelope uses `"source":"bot"` (not `"source":"client"`). This is the only module that does so.

## Dependency

Requires both `viverse-unity-matchmaking` and `viverse-unity-multiplayer` skills. No additional enable flag is needed — `GeneralModule` is active once `MultiplayerClient.Init()` returns.

## Read Order

1. This file (API + compliance gates)
2. [patterns/general-module-pattern.md](patterns/general-module-pattern.md)

## Prerequisites

1. Unity 2021.2+ with .NET 4.x runtime
2. App ID from [VIVERSE Studio](https://studio.viverse.com/)
3. Active room from `MatchmakingClient`
4. `MultiplayerClient.Initialize` + `Init` completed — no additional module option needed

## Module Access

```csharp
// After Initialize + Init complete — no enable flag required
var mp = MultiplayerClient.Instance;
GeneralModule general = mp.General;

// Init options do NOT need a general field:
var options = new MultiplayerInitOptions
{
    modules = new ModulesConfig
    {
        // game, networkSync, actionSync, leaderboard as needed
        // general is always active — omit it here
    }
};
await mp.Init(options);
```

## API Reference

### Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `SendMessage(string data)` | `void` | Broadcast an arbitrary JSON payload to all peers. `data` must be valid JSON (object, array, or a quoted string). It is embedded directly into the envelope's `data` field. |

### Events

| Event | Signature | When |
|-------|-----------|------|
| `OnMessage` | `Action<string>` | A peer sent a general message. Fires with the serialized `data` field from the envelope (not the full envelope). Self-originated messages are filtered. |
| `OnClientConnected` | `Action<string>` | A new peer connected to the room. Fires with the peer's `user_id` string directly (not JSON). |
| `OnClientDisconnected` | `Action<string>` | A peer disconnected from the room. Fires with the peer's `user_id` string directly (not JSON). |

## Message Protocol

### Outbound (client to server/peers)

```json
{
  "source": "bot",
  "messageType": "bot",
  "type": "general",
  "event": "general",
  "user_id": "<PeerId>",
  "data": {"chat": "hello!"}
}
```

Note: `"source":"bot"` is unique to this module. All other modules use `"source":"client"`.

### Inbound general message

```json
{
  "source": "bot",
  "type": "general",
  "event": "general",
  "user_id": "<sender-PeerId>",
  "data": {"chat": "hello!"}
}
```

`OnMessage` receives the value of `data` serialized as a JSON string — i.e., `"{\"chat\":\"hello!\"}"` — not the full envelope.

### Inbound connection events

```json
{
  "type": "game",
  "event": "client_connected",
  "user_id": "<peer-PeerId>"
}
```

`OnClientConnected` and `OnClientDisconnected` receive the `user_id` string directly (e.g., `"peer-abc123"`).

## Typical Integration Flow

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using ViverseSDK;

public class ChatController : MonoBehaviour
{
    private MultiplayerClient _mp;

    // Track connected peers
    private HashSet<string> _connectedPeers = new HashSet<string>();

    public void Initialize(MultiplayerClient mp)
    {
        _mp = mp;

        // Subscribe BEFORE calling SendMessage.
        _mp.General.OnMessage            += HandleMessage;
        _mp.General.OnClientConnected    += HandlePeerConnected;
        _mp.General.OnClientDisconnected += HandlePeerDisconnected;

        // Own peer is already connected.
        _connectedPeers.Add(_mp.PeerId);
    }

    // Send a chat message.
    public void SendChat(string text)
    {
        // Encode the chat text as a JSON object.
        string safeText = text.Replace("\"", "\\\"");
        string data = $"{{\"chat\":\"{safeText}\"}}";
        _mp.General.SendMessage(data);
        Debug.Log($"[General] Sent chat: {text}");
    }

    // Send a plain string (must be wrapped in escaped quotes to form valid JSON).
    public void SendPing()
    {
        _mp.General.SendMessage("\"ping\"");
    }

    // Send a structured custom event.
    public void SendItemPickup(string itemId, int quantity)
    {
        string data = $"{{\"event\":\"item_pickup\",\"item\":\"{itemId}\",\"qty\":{quantity}}}";
        _mp.General.SendMessage(data);
    }

    // Receive a message from a peer.
    private void HandleMessage(string dataJson)
    {
        // dataJson is the content of the envelope's "data" field — parse it directly.
        var payload = MiniJson.Deserialize(dataJson) as Dictionary<string, object>;
        if (payload == null)
        {
            // Peer may have sent a plain quoted string — dataJson will be e.g. "\"ping\""
            Debug.Log($"[General] Received: {dataJson}");
            return;
        }

        if (payload.ContainsKey("chat"))
        {
            Debug.Log($"[General] Chat: {payload["chat"]}");
        }
        else if (payload.ContainsKey("event"))
        {
            string evt = payload["event"].ToString();
            switch (evt)
            {
                case "item_pickup":
                    string item = payload.ContainsKey("item") ? payload["item"].ToString() : "";
                    int qty = payload.ContainsKey("qty") ? Convert.ToInt32(payload["qty"]) : 0;
                    Debug.Log($"[General] Peer picked up {qty}x {item}");
                    break;
                default:
                    Debug.Log($"[General] Unknown event: {evt}");
                    break;
            }
        }
    }

    // Peer joined.
    private void HandlePeerConnected(string userId)
    {
        _connectedPeers.Add(userId);
        Debug.Log($"[General] Peer connected: {userId} ({_connectedPeers.Count} total)");
    }

    // Peer left.
    private void HandlePeerDisconnected(string userId)
    {
        _connectedPeers.Remove(userId);
        Debug.Log($"[General] Peer disconnected: {userId} ({_connectedPeers.Count} total)");
    }

    private void OnDestroy()
    {
        if (_mp != null)
        {
            _mp.General.OnMessage            -= HandleMessage;
            _mp.General.OnClientConnected    -= HandlePeerConnected;
            _mp.General.OnClientDisconnected -= HandlePeerDisconnected;
        }
    }
}
```

## Mandatory Compliance Gates (MUST PASS)

1. **MUST** complete `viverse-unity-multiplayer` bootstrap before accessing `mp.General`.
2. **MUST NOT** add a `general` field to `ModulesConfig`. `GeneralModule` has no enable flag and is always active.
3. **MUST** pass valid JSON as the `data` argument to `SendMessage()`. A bare C# string (e.g., `"hello"`) is not valid JSON — wrap it as `"\"hello\""` to produce a JSON string value.
4. **MUST NOT** assume `OnMessage` receives the full envelope — it receives only the serialized `data` field.
5. **MUST** note that the outbound envelope uses `"source":"bot"` (unique to this module). Do not manually construct envelopes with `"source":"client"` for General.
6. **MUST** subscribe to `OnMessage`, `OnClientConnected`, and `OnClientDisconnected` before any peers send data or connect.
7. **MUST NOT** add WebSocket or WebRTC logic in C#. `GeneralModule` is a thin passthrough over `MultiplayerClient`.
8. **SHOULD** unsubscribe from all three events in `OnDestroy` to avoid dangling handler references.
9. **SHOULD** sanitize user-provided text (escape double-quotes) before embedding in a JSON string field.
10. **SHOULD** type-dispatch incoming `data` payloads by an `event` or `type` field to keep the handler extensible.

## Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| `SendMessage("hello")` — invalid JSON | Use `SendMessage("\"hello\"")` to produce a valid JSON string |
| `OnMessage` handler receives null | `data` argument is not valid JSON — verify sender constructs it correctly |
| `OnMessage` fires with full envelope | Wrong — it fires with only the `data` field value. Inspect actual payload |
| `OnClientConnected` not firing | Subscribe before `Init()` completes; first peer may arrive immediately |
| Trying to add `general` to `ModulesConfig` | Remove it — no field exists for `general` in `ModulesConfig` |
| Own SendMessage triggers OnMessage | It does not — self-originated messages are filtered by the module |
| Source field wrong in custom envelope | Do not hand-craft envelopes. Use `mp.General.SendMessage(data)` only |

## Cross-Module Comparison: General vs Typed Modules

| Aspect | General Module | Typed Modules (Game, NetworkSync, ActionSync, Leaderboard) |
|--------|---------------|-----------------------------------------------------------|
| Enable flag | None — always active | Requires `ModulesConfig.<module> = new ModuleOption { enabled = true }` |
| Outbound source | `"source":"bot"` | `"source":"client"` |
| Data schema | Freeform JSON (`data` field) | Fixed schema (score, position, action_name, etc.) |
| Routing key | `type:"general"`, `event:"general"` | Module-specific type + event strings |
| OnMessage payload | Serialized `data` field only | Full envelope or structured fields depending on module |
| Connection tracking | Yes (`OnClientConnected`, `OnClientDisconnected`) | No |
| Best for | Chat, custom events, extensions | Typed real-time game data |
