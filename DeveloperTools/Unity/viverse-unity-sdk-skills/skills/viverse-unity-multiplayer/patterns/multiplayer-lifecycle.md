# Multiplayer Lifecycle Pattern

## Connection Flow

```
MatchmakingClient.OnJoinRoom(roomId)
         │
         ▼
MultiplayerClient.Initialize(roomId, appId, userSessionId)
         │
         ├── WebGL: jslib → new window.play.MultiplayerClient() → Mediasoup connect
         │
         └── Editor: NativeWebSocket → proxy URL with roomId + peerId
         │
         ▼
   OnConnected fires
         │
         ▼
MultiplayerClient.Init(options)   ← Creates game room, activates modules
         │
         ▼
   ┌─────────────────────────────┐
   │     ACTIVE SESSION          │
   │  Module events flowing      │
   │  Send/receive data          │
   │  OnClientConnected/Left     │
   └──────────────┬──────────────┘
                  │
                  ▼
   MultiplayerClient.Disconnect()
         │
         ▼
   OnDisconnected fires
```

## Dual-Path Structure (C#)

```csharp
public class MultiplayerClient : MonoBehaviour
{
    public static MultiplayerClient Instance { get; private set; }

    // === WebGL DllImport declarations ===
#if UNITY_WEBGL && !UNITY_EDITOR
    [DllImport("__Internal")]
    private static extern void PlaySDK_Multiplayer_Initialize(int requestId, string roomId, string appId, string userSessionId, string gameObjectName, string callbackMethodName);

    [DllImport("__Internal")]
    private static extern void PlaySDK_Multiplayer_Init(int requestId, string jsonOptions);

    [DllImport("__Internal")]
    private static extern void PlaySDK_Multiplayer_Send(string data);

    [DllImport("__Internal")]
    private static extern void PlaySDK_Multiplayer_Disconnect();

    [DllImport("__Internal")]
    private static extern void PlaySDK_Multiplayer_SetMaster(bool isMaster);

    [DllImport("__Internal")]
    private static extern bool PlaySDK_Multiplayer_IsMasterUser();

    [DllImport("__Internal")]
    private static extern void PlaySDK_Multiplayer_GetRoomInfo(int requestId);
#endif

    // === Shared: modules, events, properties ===
    public GameModule Game { get; private set; }
    public NetworkSyncModule NetworkSync { get; private set; }
    public ActionSyncModule ActionSync { get; private set; }
    public LeaderboardModule Leaderboard { get; private set; }
    public LambdaModule Lambda { get; private set; }
    public GeneralModule General { get; private set; }

    public event Action OnConnected;
    public event Action OnDisconnected;
    public event Action<string> OnClientConnected;
    public event Action<string> OnClientDisconnected;
    public event Action<string> OnMessage;

    public string RoomId { get; private set; }
    public string AppId { get; private set; }
    public string PeerId { get; private set; }

    // === Initialize: connect to service ===
    public async Task Initialize(string roomId, string appId, string userSessionId = null)
    {
        RoomId = roomId;
        AppId = appId;
        PeerId = string.IsNullOrEmpty(userSessionId) ? Guid.NewGuid().ToString() : userSessionId;

#if UNITY_WEBGL && !UNITY_EDITOR
        // WebGL: jslib handles Mediasoup connection
        var tcs = new TaskCompletionSource<string>();
        int requestId = ++_requestCounter;
        _pendingRequests[requestId] = tcs;
        PlaySDK_Multiplayer_Initialize(requestId, roomId, appId, PeerId, gameObject.name, nameof(ReceiveMessageFromJS));
        await tcs.Task;
#else
        // Editor: NativeWebSocket to proxy
        var url = $"{PROXY_URL}/api/webrtcproxy-service/v1/ws?roomId={roomId}&peerId={PeerId}&pod=0";
        _ws = new WebSocket(url);
        _ws.OnOpen += () => OnConnected?.Invoke();
        _ws.OnMessage += (bytes) => HandleProxyMessage(Encoding.UTF8.GetString(bytes));
        _ws.Connect();
#endif

        // Initialize modules (both paths)
        Game = new GameModule(this);
        NetworkSync = new NetworkSyncModule(this);
        ActionSync = new ActionSyncModule(this);
        Leaderboard = new LeaderboardModule(this);
        Lambda = new LambdaModule(this);
        General = new GeneralModule(this);
    }
}
```

## Singleton Pattern

```csharp
private void Awake()
{
    if (Instance != null && Instance != this) { Destroy(gameObject); return; }
    Instance = this;
    DontDestroyOnLoad(gameObject);
}

private void OnDestroy()
{
    if (Instance == this) Instance = null;  // MUST null for reconnection
}
```

## Editor Path: Update Loop

```csharp
#if !UNITY_WEBGL || UNITY_EDITOR
private void Update()
{
    _ws?.DispatchMessageQueue();  // NativeWebSocket requires this
}
#endif
```

## Request-Response Pattern (WebGL)

```csharp
private int _requestCounter;
private Dictionary<int, TaskCompletionSource<string>> _pendingRequests = new();

public async Task<string> GetRoomInfo()
{
    var tcs = new TaskCompletionSource<string>();
    int requestId = ++_requestCounter;
    _pendingRequests[requestId] = tcs;
    PlaySDK_Multiplayer_GetRoomInfo(requestId);
    return await tcs.Task;
}

// Called by jslib via SendMessage
public void ReceiveMessageFromJS(string json)
{
    var parsed = MiniJson.Deserialize(json) as Dictionary<string, object>;
    if (parsed.ContainsKey("requestId"))
    {
        int requestId = Convert.ToInt32(parsed["requestId"]);
        _pendingRequests[requestId].TrySetResult(parsed["data"].ToString());
        _pendingRequests.Remove(requestId);
    }
}
```
