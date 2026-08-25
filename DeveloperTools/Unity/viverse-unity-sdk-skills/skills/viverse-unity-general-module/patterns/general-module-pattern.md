# Pattern: Chat and Custom Messaging

Use `GeneralModule` to implement in-room chat and arbitrary typed custom messages between peers.

## Problem

Multiplayer games need a side-channel for messages that do not fit the typed modules (position, action, score). Chat, custom events, and freeform data all need a single consistent pathway to all peers in the room.

## Solution

Use `mp.General.SendMessage(json)` to broadcast JSON to all peers. Dispatch on a `type` or `event` field inside your payload. Listen to `OnClientConnected` and `OnClientDisconnected` for roster management.

## When To Use

- Text chat visible to all players in the room
- Custom game events: item pickups, door triggers, player emotes
- Game state snapshots sent to a joining peer (catch-up sync)
- Any message type that is not continuous position sync, discrete competitive action, or score

## Code Template

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using ViverseSDK;

/// <summary>
/// Handles chat and custom messages using GeneralModule.
/// Setup() must be called after MultiplayerClient.Init() returns.
/// </summary>
public class GeneralMessenger : MonoBehaviour
{
    private MultiplayerClient _mp;
    private List<string> _connectedPeers = new List<string>();

    // UI callback: (senderUserId, messageText)
    public event Action<string, string> OnChatReceived;

    public void Setup(MultiplayerClient mp)
    {
        _mp = mp;

        _mp.General.OnMessage            += HandleMessage;
        _mp.General.OnClientConnected    += HandleConnected;
        _mp.General.OnClientDisconnected += HandleDisconnected;
    }

    // ----- Outbound --------------------------------------------------------

    public void SendChat(string text)
    {
        // Escape quotes inside text to keep JSON valid.
        string safe = text.Replace("\\", "\\\\").Replace("\"", "\\\"");
        _mp.General.SendMessage($"{{\"type\":\"chat\",\"text\":\"{safe}\"}}");
    }

    public void SendCustomEvent(string eventName, string payloadJson)
    {
        // payloadJson must itself be valid JSON, e.g. "{\"item\":\"sword\"}"
        _mp.General.SendMessage($"{{\"type\":\"{eventName}\",\"data\":{payloadJson}}}");
    }

    // To send a plain string (rare — prefer objects):
    // _mp.General.SendMessage("\"ping\"");

    // ----- Inbound --------------------------------------------------------

    private void HandleMessage(string dataJson)
    {
        // dataJson is the value of the "data" field from the inbound envelope.
        var payload = MiniJson.Deserialize(dataJson) as Dictionary<string, object>;
        if (payload == null)
        {
            Debug.Log($"[General] Raw string received: {dataJson}");
            return;
        }

        string msgType = payload.ContainsKey("type") ? payload["type"].ToString() : "";
        switch (msgType)
        {
            case "chat":
                string text = payload.ContainsKey("text") ? payload["text"].ToString() : "";
                Debug.Log($"[Chat] {text}");
                OnChatReceived?.Invoke("peer", text);
                break;

            default:
                // Forward to a custom event handler if you have one.
                Debug.Log($"[General] Event '{msgType}': {dataJson}");
                break;
        }
    }

    private void HandleConnected(string userId)
    {
        if (!_connectedPeers.Contains(userId))
            _connectedPeers.Add(userId);
        Debug.Log($"[General] Connected: {userId} — peers: {_connectedPeers.Count}");
    }

    private void HandleDisconnected(string userId)
    {
        _connectedPeers.Remove(userId);
        Debug.Log($"[General] Disconnected: {userId} — peers: {_connectedPeers.Count}");
    }

    private void OnDestroy()
    {
        if (_mp != null)
        {
            _mp.General.OnMessage            -= HandleMessage;
            _mp.General.OnClientConnected    -= HandleConnected;
            _mp.General.OnClientDisconnected -= HandleDisconnected;
        }
    }
}
```

## Integration Points

| Hook | When |
|------|------|
| `Setup(mp)` | After `mp.Init()` returns — before any peers can connect |
| `SendChat(text)` | On player submitting a chat message |
| `SendCustomEvent(name, payloadJson)` | On game event (item pickup, trigger, etc.) |
| `OnChatReceived` | Subscribe in UI component to display chat bubbles |
| `HandleConnected` | Update player roster / presence indicators |
| `HandleDisconnected` | Remove player from roster / clean up their state |

## Critical Notes

- **`data` is embedded raw JSON** — `SendMessage("{\"x\":1}")` produces `"data":{"x":1}` in the envelope. A bare string like `SendMessage("hello")` is invalid JSON and will corrupt the payload. Use `SendMessage("\"hello\"")` for a string value.
- **`OnMessage` payload** — receives only the `data` field value, not the full envelope. If you sent `{"type":"chat","text":"hi"}`, the handler receives `"{\"type\":\"chat\",\"text\":\"hi\"}"`.
- **`source:"bot"` is automatic** — `GeneralModule` sets `"source":"bot"` in the outbound envelope. Do not try to set it yourself.
- **No enable flag** — do not add `general = new ModuleOption { ... }` to `ModulesConfig`. The module is always active.
- **Self-filter** — `OnMessage` does not fire for your own `SendMessage` calls. Update your own UI state locally.
