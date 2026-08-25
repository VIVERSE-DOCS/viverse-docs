# Dual-Path Architecture Pattern

## Overview

The VIVERSE Unity SDK uses conditional compilation to run two completely different transport paths from a single C# API surface:

- **WebGL path** (`#if UNITY_WEBGL && !UNITY_EDITOR`): C# → ViversePlay.jslib → viverse-sdk UMD (which loads play-sdk internally)
- **Editor path** (`#else`): C# → NativeWebSocket → backend directly

Both paths expose the **same public API** — callers never know which path is active.

## Structure

```csharp
namespace ViverseSDK
{
    public class MatchmakingClient : MonoBehaviour
    {
        // === Shared: events, public API, singleton ===
        public event Action OnConnect;
        public event Action OnDisconnect;
        public event Action<string> OnJoinRoom;
        // ...

#if UNITY_WEBGL && !UNITY_EDITOR
        // === WebGL: DllImport declarations only ===
        [DllImport("__Internal")]
        private static extern void ViversePlay_Matchmaking_Initialize(string appId, bool debugMode, string gameObjectName, string callbackMethod);
#endif

        // === Shared fields (used by both paths) ===
        private string _appId;
        private string _sessionId;
        private bool _isConnected;
        private Dictionary<int, Action<string>> _pendingRequests;

#if !UNITY_WEBGL || UNITY_EDITOR
        // === Editor: WebSocket + protocol state ===
        private WebSocket _ws;
        private bool _debugMode;
        private Room _currentRoom;
        private Actor _currentActor;
        private static readonly byte[] _xorKey = { 0x12, 0x34, 0x56, 0x78 };
#endif
    }
}
```

## Key Rules

### 1. Shared fields go OUTSIDE `#if` blocks

```csharp
// CORRECT — these are used in both paths
private string _appId;
private string _sessionId;
private bool _isConnected;
private Dictionary<int, Action<string>> _pendingRequests;

#if !UNITY_WEBGL || UNITY_EDITOR
private WebSocket _ws;  // Editor-only
private Room _currentRoom;
private Actor _currentActor;
#endif
```

### 2. Public methods contain both paths inline

```csharp
public void Initialize(string appId, bool debugMode = false)
{
    _appId = appId;
    _sessionId = System.Guid.NewGuid().ToString();  // Client-generated UUID
#if UNITY_WEBGL && !UNITY_EDITOR
    ViversePlay_Matchmaking_Initialize(appId, debugMode, gameObject.name, nameof(OnJSMessage));
#else
    // Full Editor connection logic here
    var url = $"{DEFAULT_MATCHMAKING_URL}/api/matchmaking-service/v1/match?app_id={appId}";
    if (debugMode) url += "&debug=true";
    _ws = new WebSocket(url);
    _ws.OnOpen += () => { _isConnected = true; OnConnect?.Invoke(); StartHeartbeat(); };
    _ = _ws.Connect();
#endif
}
```

### 3. SendRequest auto-injects protocol fields

```csharp
public void SendRequest(string requestType, string jsonPayload = "{}", Action<string> callback = null)
{
    int requestId = ++_requestCounter;
    if (callback != null) _pendingRequests[requestId] = callback;

    // Auto-injects: request_id, request_type, app_id, session_id
    string json = $"{{\"request_id\":{requestId},\"request_type\":\"{requestType}\",\"app_id\":\"{_appId}\",\"session_id\":\"{_sessionId}\",{inner}}}";

#if UNITY_WEBGL && !UNITY_EDITOR
    ViversePlay_Matchmaking_SendRequest(json);
#else
    byte[] data = System.Text.Encoding.UTF8.GetBytes(json);
    if (!_debugMode) data = XorEncrypt(data);
    _ = _ws.Send(data);
#endif
}
```

### 4. WebGL receives messages via SendMessage callback

```csharp
// jslib calls: SendMessage(gameObjectName, "OnJSMessage", json)
public void OnJSMessage(string json)
{
    ProcessIncomingMessage(json);
}
```

### 5. Editor receives messages via WebSocket event

```csharp
#if !UNITY_WEBGL || UNITY_EDITOR
_ws.OnMessage += (bytes) => {
    string json = _debugMode
        ? System.Text.Encoding.UTF8.GetString(bytes)
        : System.Text.Encoding.UTF8.GetString(XorEncrypt(bytes));
    ProcessIncomingMessage(json);  // Same processing as WebGL path
};
#endif
```

### 6. Message processing is SHARED

```csharp
// Both paths converge here — no #if needed
private void ProcessIncomingMessage(string json)
{
    if (json.Contains("\"event\":"))
    {
        var payload = JsonUtility.FromJson<EventPayload>(json);
        DispatchEvent(payload.@event, json);
        return;
    }
    // Handle request-response
    var response = JsonUtility.FromJson<MatchResponse>(json);
    if (_pendingRequests.ContainsKey(response.request_id))
    {
        _pendingRequests[response.request_id]?.Invoke(json);
        _pendingRequests.Remove(response.request_id);
    }
}
```

## WebGL jslib Side

The jslib creates the matchmaking client via viverse-sdk's `Play` class (which internally loads play-sdk UMD from CDN):

```javascript
var $ViversePlay_State = { client: null, gameObjectName: null, callbackMethod: null };

ViversePlay_Matchmaking_Initialize__deps: ['$ViversePlay_State'],
ViversePlay_Matchmaking_Initialize: function(appIdPtr, debugMode, goNamePtr, cbNamePtr) {
    var appId = UTF8ToString(appIdPtr);
    ViversePlay_State.gameObjectName = UTF8ToString(goNamePtr);
    ViversePlay_State.callbackMethod = UTF8ToString(cbNamePtr);

    (async function() {
        // viverse-sdk exports Play as a class — must use `new`
        var PlaySDK = globalThis.viverse.Play || globalThis.viverse.play;
        var playClient = new PlaySDK();
        // This awaits internal loading of play-sdk UMD from CDN
        var client = await playClient.newMatchmakingClient(appId, debugMode);
        ViversePlay_State.client = client;

        // Subscribe to events and forward via SendMessage
        client.on("onConnect", function() {
            var payload = JSON.stringify({ event: "onConnect", data: null });
            SendMessage(ViversePlay_State.gameObjectName, ViversePlay_State.callbackMethod, payload);
        });
        // ... other events
    })();
}
```

**Critical**: `new PlaySDK()` not `PlaySDK()` — it's a class constructor, not a factory function. Using it as a factory throws `"Class constructor ce cannot be invoked without 'new'"`.

## Singleton Pattern

```csharp
private void Awake()
{
    if (Instance != null && Instance != this) { Destroy(gameObject); return; }
    Instance = this;
    DontDestroyOnLoad(gameObject);
}

// CRITICAL: Clear instance on destroy for reconnection support
private void OnDestroy()
{
    if (Instance == this) Instance = null;
}
```

## NativeWebSocket Requirement (Editor)

The Editor path requires the `NativeWebSocket` plugin which provides:
- `WebSocket` class with async Connect/Close/Send
- `DispatchMessageQueue()` — must be called in `Update()` to deliver callbacks on main thread

```csharp
#if !UNITY_WEBGL || UNITY_EDITOR
private void Update()
{
    _ws?.DispatchMessageQueue();
}
#endif
```

## IsConnected Tracking

`_isConnected` is a **shared field** (outside `#if` blocks) tracked by both paths:

- **Editor**: Set `true` in `OnOpen`, `false` in `OnClose`
- **WebGL**: Set `true`/`false` in `DispatchEvent("onConnect"/"onDisconnect")`

```csharp
private void DispatchEvent(string eventName, string json)
{
    switch (eventName)
    {
        case "onConnect":
            _isConnected = true;
            OnConnect?.Invoke();
            break;
        case "onDisconnect":
            _isConnected = false;
            OnDisconnect?.Invoke();
            break;
        // ...
    }
}
```
