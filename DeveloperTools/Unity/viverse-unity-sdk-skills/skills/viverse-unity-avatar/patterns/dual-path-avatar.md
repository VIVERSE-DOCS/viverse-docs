# Dual-Path Avatar Architecture

## Overview

AvatarClient uses conditional compilation to select the correct transport:

```
┌──────────────────────────────────────────────────────────────┐
│  AvatarClient methods (GetProfile, GetAvatarList, etc.)       │
├──────────────────────────────────────────────────────────────┤
│  #if UNITY_WEBGL && !UNITY_EDITOR                            │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  CallWebGL() helper                                    │  │
│  │  1. Register TaskCompletionSource in registry          │  │
│  │  2. Call jslib function (fetch-based)                   │  │
│  │  3. jslib: fetch → parse → SendMessage                 │  │
│  │  4. AuthManager.OnAvatarCallback → resolve TCS         │  │
│  └────────────────────────────────────────────────────────┘  │
│  #else                                                        │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Direct UnityWebRequest                                │  │
│  │  1. Get/GetNoAuth/DownloadBytes helpers                │  │
│  │  2. SetRequestHeader("AccessToken", token)             │  │
│  │  3. Parse response, return AvatarResult                │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

## WebGL Path — CallWebGL Helper

All profile/list operations share a common bridge pattern:

```csharp
private async Task<AvatarResult> CallWebGL(string method, Action<int, string, string> invoke)
{
    var tcs = new TaskCompletionSource<string>();
    int bridgeId = AvatarCallbackRegistry.Register(tcs);
    string receiverName = AuthManager.Instance.gameObject.name;
    invoke(bridgeId, receiverName, "OnAvatarCallback");
    string resultJson = await tcs.Task;
    // Parse success/data/error from result
}
```

## WebGL Path — Binary Download (Special)

VRM download differs from other APIs because it transfers binary data:

```csharp
// 1. Download via jslib (may decrypt via Avatar SDK)
ViverseAvatar_DownloadAvatarFile(bridgeId, go, cb, vrmUrl, token);

// 2. jslib stores bytes in downloadedBuffers[bridgeId]
// 3. SendMessage returns {bridgeId, success, size}

// 4. C# allocates buffer and copies from JS heap
byte[] buffer = new byte[size];
GCHandle handle = GCHandle.Alloc(buffer, GCHandleType.Pinned);
try {
    int copied = ViverseAvatar_CopyDownloadedBytes(bridgeId, handle.AddrOfPinnedObject(), size);
} finally {
    handle.Free();
}
```

### Why Two-Step (Download then Copy)?

JavaScript `SendMessage` only passes strings. Binary data cannot be sent via `SendMessage`. So:
1. JS downloads and stores the `Uint8Array` in a dictionary keyed by `bridgeId`
2. `SendMessage` notifies C# with the size
3. C# calls back into JS via `CopyDownloadedBytes` with a pinned pointer
4. JS writes to WASM heap: `HEAPU8.set(buf.subarray(0, copyLen), destPtr)`
5. JS frees its copy: `delete downloadedBuffers[bridgeId]`

## jslib Structure (ViverseAvatar.jslib)

Shared helpers via `$Avatar_Helpers`:

```javascript
var Avatar_Helpers = {
    avatarSdkUrl: 'https://avatar.viverse.com/static-misc/avatar-js-sdk/1.1.1/Avatar-SDK.js',
    avatarSdkLoaded: false,
    avatarSdkInstance: null,
    downloadedBuffers: {},  // bridgeId → Uint8Array (VRM bytes)

    resolveUrl: function(path) {
        // localhost → proxy, otherwise → production
        if (window.location.hostname === 'localhost')
            return window.location.origin + '/api/avatar/' + path;
        return 'https://sdk-api.viverse.com/' + path;
    },

    resolveFileUrl: function(url) {
        // Proxy avatar.viverse.com URLs for localhost
        if (window.location.hostname !== 'localhost') return url;
        if (url.indexOf('https://avatar.viverse.com/') === 0)
            return window.location.origin + '/api/avatar-files/' + url.substring(27);
        return url;
    },

    makeHeaders: function(token) {
        var h = { 'Content-Type': 'application/json' };
        if (token) h['AccessToken'] = token;
        return h;
    },

    sendResult: function(go, cb, bridgeId, success, data, message) {
        var payload = { bridgeId: bridgeId, success: success };
        if (data !== undefined && data !== null) payload.data = data;
        if (message) payload.message = message;
        SendMessage(go, cb, JSON.stringify(payload));
    },

    loadAvatarSdk: function() {
        // Dynamically loads Avatar SDK script for VRM decryption
        // Returns Promise<AvatarSDK instance | null>
    }
};
```

## Editor Path — HTTP Helpers

Three reusable helpers handle all HTTP methods:

```csharp
// Authenticated GET
private static Task<string> Get(string url, string token, CancellationToken ct)
{
    var req = UnityWebRequest.Get(url);
    req.SetRequestHeader("Content-Type", "application/json");
    req.SetRequestHeader("AccessToken", token);
    // ... SendWebRequest + Task completion
}

// Public GET (no auth)
private static Task<string> GetNoAuth(string url, CancellationToken ct)
{
    var req = UnityWebRequest.Get(url);
    req.SetRequestHeader("Content-Type", "application/json");
    // ... no AccessToken header
}

// Binary download
private static Task<byte[]> DownloadBytes(string url, string token, CancellationToken ct)
{
    var req = UnityWebRequest.Get(url);
    if (!string.IsNullOrEmpty(token))
        req.SetRequestHeader("AccessToken", token);
    // Returns req.downloadHandler.data
}
```

## GetActiveAvatar — Helper Logic (Shared)

`GetActiveAvatar` is implemented in C# (not jslib) because it's a convenience wrapper:

```csharp
public async Task<AvatarResult> GetActiveAvatar(string token, CancellationToken ct)
{
    // 1. Call GetAvatarList
    var listResult = await GetAvatarList(token, ct);
    if (!listResult.success) return listResult;

    // 2. Parse response
    var root = MiniJson.Deserialize(listResult.data);
    // Response: {"version":"...","data":{"CurrentAvatarId":N,"Avatars":[...]}}

    // 3. Extract CurrentAvatarId
    string currentId = data["CurrentAvatarId"]?.ToString();

    // 4. Find matching avatar in Avatars array
    foreach (var avatar in avatarList)
        if (avatar["id"]?.ToString() == currentId)
            return new AvatarResult { success = true, data = MiniJson.Serialize(avatar) };
}
```

## AvatarCallbackRegistry

Static registry maps bridgeId → TaskCompletionSource. Same pattern as CloudSave/Leaderboard:

```csharp
public static class AvatarCallbackRegistry
{
    private static int _counter;
    private static readonly ConcurrentDictionary<int, TaskCompletionSource<string>> _pending = new();

    public static int Register(TaskCompletionSource<string> tcs);
    public static void Resolve(int bridgeId, string json);
    public static void Reject(int bridgeId, string error);
}
```

## AuthManager Callback Receiver

`AuthManager.OnAvatarCallback(string json)` receives all Avatar callbacks:

```csharp
public void OnAvatarCallback(string json)
{
    var dict = MiniJson.Deserialize(json) as Dictionary<string, object>;
    int bridgeId = Convert.ToInt32(dict["bridgeId"]);
    AvatarCallbackRegistry.Resolve(bridgeId, json);
}
```

## Key Differences from Other Features

| Aspect | Avatar | CloudSave/Leaderboard |
|--------|--------|----------------------|
| App ID | NOT needed | Required |
| Public APIs | Yes (no auth) | No |
| Binary transfer | Yes (VRM download) | No |
| External SDK | Avatar SDK (decrypt) | None / Ironhide |
| Base URL | sdk-api.viverse.com | BCGW / viveport.com |
