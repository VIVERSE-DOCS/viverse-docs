# Module Messaging Pattern

## Overview

MultiplayerClient uses 6 modules that communicate via a standardized JSON protocol over the data channel (WebGL) or proxy WebSocket (Editor).

## Message Format

### Outbound (C# → server/peers)

```json
{
  "source": "client",
  "messageType": "bot",
  "type": "<module-type>",
  "event": "<event-name>",
  "user_id": "<PeerId>",
  "data": { }
}
```

### Inbound (server/peers → C#)

```json
{
  "source": "bot",
  "type": "<module-type>",
  "event": "<event-name>",
  "user_id": "<sender-peerId>",
  "data": { }
}
```

**Module types**: `game`, `networksync`, `actionsync`, `leaderboard`, `lambda`, `general`

## Module Base Pattern

Each module follows this structure:

```csharp
public class NetworkSyncModule
{
    private readonly MultiplayerClient _client;

    // Events
    public event Action<string> OnNotifyPositionUpdate;
    public event Action<string> OnNotifyRemove;

    public NetworkSyncModule(MultiplayerClient client)
    {
        _client = client;
        // Editor path: subscribe to module messages
        _client.OnModuleMessage += HandleModuleMessage;
    }

    // Send method — constructs message and sends via client
    public void SendPosition(string dataJson)
    {
        var msg = new Dictionary<string, object>
        {
            ["source"] = "client",
            ["messageType"] = "bot",
            ["type"] = "networksync",
            ["event"] = "position_update",
            ["user_id"] = _client.PeerId,
            ["data"] = MiniJson.Deserialize(dataJson)
        };
        _client.Send(MiniJson.Serialize(msg));
    }

    // Editor path: route incoming messages
    private void HandleModuleMessage(string moduleType, Dictionary<string, object> data)
    {
        if (moduleType != "networksync") return;

        string userId = data.ContainsKey("user_id") ? data["user_id"].ToString() : "";
        if (userId == _client.PeerId) return;  // FILTER SELF

        string eventName = data["event"].ToString();
        string dataJson = MiniJson.Serialize(data);

        switch (eventName)
        {
            case "notify_position_update":
                OnNotifyPositionUpdate?.Invoke(dataJson);
                break;
            case "notify_remove":
                OnNotifyRemove?.Invoke(dataJson);
                break;
        }
    }

    // WebGL path: triggered by DispatchEvent in MultiplayerClient
    internal void TriggerOnNotifyPositionUpdate(string json) => OnNotifyPositionUpdate?.Invoke(json);
    internal void TriggerOnNotifyRemove(string json) => OnNotifyRemove?.Invoke(json);
}
```

## Self-Message Filtering (CRITICAL)

Every module MUST filter messages where `user_id == PeerId`:

```csharp
string userId = data.ContainsKey("user_id") ? data["user_id"].ToString() : "";
if (userId == _client.PeerId) return;  // Don't process own messages
```

## WebGL Event Routing

In WebGL path, jslib forwards events as `{"eventName":"<prefix>/<event>","data":{...}}`. MultiplayerClient's `DispatchEvent` routes them:

```csharp
private void DispatchEvent(string eventName, string dataJson)
{
    switch (eventName)
    {
        // NetworkSync
        case "networksync/onNotifyPositionUpdate":
            NetworkSync?.TriggerOnNotifyPositionUpdate(dataJson);
            break;

        // ActionSync
        case "actionsync/onCompetition":
            ActionSync?.TriggerOnCompetition(dataJson);
            break;

        // Game lifecycle
        case "game/onCountdownToStart":
            Game?.TriggerEvent("onCountdownToStart", dataJson);
            break;

        // ... other events
    }
}
```

## Lambda Module (Special Case)

Lambda is unique — it uses REST API calls, not the data channel. It has its own async bridge:

### WebGL Path (via jslib)
```csharp
public async Task<string> CreateJob(string gameId, string eventName, string data, string requestId, string token)
{
    int bridgeId = LambdaBridge.Register();
    PlaySDK_Lambda_CreateJob(bridgeId, gameObject.name, "ReceiveMessageFromJS", gameId, eventName, data, requestId, token);
    return await LambdaBridge.GetTask(bridgeId);
}
```

### Editor Path (direct REST)
```csharp
public async Task<string> CreateJob(string gameId, string eventName, string data, string requestId, string token)
{
    // Direct HTTP call to Lambda service
    using var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Authorization", $"Bearer {token}");
    var response = await client.PostAsync(LAMBDA_URL, content);
    return await response.Content.ReadAsStringAsync();
}
```

## LambdaBridge (Static Registry)

Bridges jslib async callbacks to C# `Task<string>`:

```csharp
public static class LambdaBridge
{
    private static int _counter;
    private static Dictionary<int, TaskCompletionSource<string>> _pending = new();

    public static int Register()
    {
        int id = ++_counter;
        _pending[id] = new TaskCompletionSource<string>();
        return id;
    }

    public static Task<string> GetTask(int id) => _pending[id].Task;

    public static void Resolve(int id, string result)
    {
        if (_pending.TryGetValue(id, out var tcs))
        {
            tcs.TrySetResult(result);
            _pending.Remove(id);
        }
    }

    public static void Reject(int id, string error)
    {
        if (_pending.TryGetValue(id, out var tcs))
        {
            tcs.TrySetException(new Exception(error));
            _pending.Remove(id);
        }
    }
}
```
