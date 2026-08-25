# Dual-Path Cloud Save Architecture

## Overview

CloudSaveClient uses conditional compilation to select the correct HTTP transport:

```
┌──────────────────────────────────────────────────────────────┐
│  CloudSaveClient methods (Save, GetAll, GetLatest, etc.)      │
├──────────────────────────────────────────────────────────────┤
│  #if UNITY_WEBGL && !UNITY_EDITOR                            │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  CallWebGL() helper                                    │  │
│  │  1. Register TaskCompletionSource in registry          │  │
│  │  2. Call jslib function (fetch-based)                   │  │
│  │  3. jslib: fetch → parse response → SendMessage        │  │
│  │  4. AuthManager.OnCloudSaveCallback → resolve TCS      │  │
│  └────────────────────────────────────────────────────────┘  │
│  #else                                                        │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Direct UnityWebRequest                                │  │
│  │  1. Post/Get/DeleteRequest helper                      │  │
│  │  2. ApplyToken (auto-detect JWT vs AuthKey)            │  │
│  │  3. Parse response, return CloudSaveResult             │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

## WebGL Path — CallWebGL Helper

All 6 operations share a common bridge pattern:

```csharp
private async Task<CloudSaveResult> CallWebGL(string method, Action<int, string, string> invoke)
{
    var tcs = new TaskCompletionSource<string>();
    int bridgeId = CloudSaveCallbackRegistry.Register(tcs);
    string receiverName = AuthManager.Instance.gameObject.name;
    invoke(bridgeId, receiverName, "OnCloudSaveCallback");
    string resultJson = await tcs.Task;
    // Parse success/data/error from result
}
```

## jslib Structure (ViverseCloudSave.jslib)

Shared helpers via `$CloudSave_Helpers`:

```javascript
var CloudSave_Helpers = {
    baseUrl: 'https://broadcasting-gateway-gaming.vrprod.viveport.com',
    userAppPrefix: '/api/webrtcbot-service/v1/userapp',
    cloudSavePrefix: '/api/webrtcbot-service/v1/cloudsave',

    makeHeaders: function(token, includeContent) {
        var h = {};
        if (includeContent) h['Content-Type'] = 'application/json';
        var isJwt = token.split('.').length === 3;
        h[isJwt ? 'AccessToken' : 'AuthKey'] = token;
        return h;
    },

    sendResult: function(go, cb, bridgeId, success, data, error) {
        var obj = { bridgeId: bridgeId, success: success };
        if (data) obj.data = data;
        if (error) obj.message = error;
        SendMessage(go, cb, JSON.stringify(obj));
    }
};
```

## Editor Path — HTTP Helpers

Three reusable helpers handle all HTTP methods:

```csharp
private static Task<string> Post(string url, string body, string token);
private static Task<string> Get(string url, string token);
private static Task<string> DeleteRequest(string url, string token);

private static void ApplyToken(UnityWebRequest req, string token)
{
    bool isJwt = token.Split('.').Length == 3;
    req.SetRequestHeader(isJwt ? "AccessToken" : "AuthKey", token);
}
```

## Key Gotchas

### 1. Version Parameter in WebGL

C# `long` is 64-bit, but Emscripten passes it as two 32-bit values, corrupting subsequent pointer parameters. Solution: pass version as `string` to jslib.

```csharp
// DllImport signature
private static extern void ViverseCloudSave_Delete(..., string version, string token);

// Call site
ViverseCloudSave_Delete(bridgeId, go, cb, _appId, version.ToString(), token);
```

### 2. Primitive Value Wrapping

Server requires JSON object body for setPlayerData. SDK auto-wraps:

```csharp
string body = dataJson ?? "{}";
if (!body.TrimStart().StartsWith("{") && !body.TrimStart().StartsWith("["))
    body = $"{{\"value\":{body}}}";
```

### 3. GetPlayerData Response Parsing

Server returns flat `{data: {key: value}}` structure. Parse by key:

```csharp
var root = MiniJson.Deserialize(response) as Dictionary<string, object>;
if (root["data"] is Dictionary<string, object> dataObj && dataObj.ContainsKey(key))
    return MiniJson.Serialize(dataObj[key]);
```

### 4. CloudSaveCallbackRegistry

Static registry maps bridgeId → TaskCompletionSource. Uses ConcurrentDictionary for thread safety:

```csharp
public static class CloudSaveCallbackRegistry
{
    private static int _counter;
    private static readonly ConcurrentDictionary<int, TaskCompletionSource<string>> _pending = new();

    public static int Register(TaskCompletionSource<string> tcs);
    public static void Resolve(int bridgeId, string json);
    public static void Reject(int bridgeId, string error);
}
```

### 5. AuthManager Callback Receiver

`AuthManager.OnCloudSaveCallback(string json)` receives all Cloud Save callbacks and routes to the registry:

```csharp
public void OnCloudSaveCallback(string json)
{
    var dict = MiniJson.Deserialize(json) as Dictionary<string, object>;
    int bridgeId = Convert.ToInt32(dict["bridgeId"]);
    CloudSaveCallbackRegistry.Resolve(bridgeId, json);
}
```
