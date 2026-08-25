# Getting started with the VIVERSE Unity SDK

Ship VIVERSE-connected Unity games to WebGL and iterate them in the Editor with the same C# API. This guide walks you through installing the SDK, authenticating a player, and calling every feature the SDK exposes — authentication, Lambda functions, matchmaking, real-time multiplayer, cloud save, leaderboards, achievements, and avatars.

---

## What you get

The SDK ships a thin C# surface on top of the viverse-sdk JavaScript library. In WebGL builds, C# calls jslib bridges that delegate to the CDN-hosted viverse-sdk. In the Editor, the same C# API talks directly to VIVERSE REST endpoints and WebSocket gateways, so you can develop and test without a browser.

| Feature | What it does |
|---|---|
| Authentication | Signs a player in with their VIVERSE account through OAuth 2.0. |
| Lambda | Invokes server-side functions you register in VIVERSE Studio. |
| Matchmaking | Creates, discovers, and joins rooms over WebSocket. |
| Multiplayer | Runs WebRTC data channels for real-time sync, action, leaderboard, and general modules. |
| Cloud Save | Persists versioned save files and key-value player data. |
| Leaderboard | Submits scores and reads rankings, with RSA/AES payload encryption. |
| Achievements | Fetches and unlocks achievements, with the same encryption pipeline. |
| Avatar | Reads a player's profile and avatar list, downloads VRM models, and (optionally) renders them via UniVRM. |

---

## Requirements

Before you install the SDK, confirm your project meets these baselines.

- Unity 2021.2 or newer with the .NET 4.x scripting runtime.
- WebGL build support installed through Unity Hub.
- A VIVERSE App ID from [VIVERSE Studio](https://studio.viverse.com/).
- Optional: [UniVRM v0.130.1 or newer](https://github.com/vrm-c/UniVRM) if you want to render downloaded VRM models. The SDK compiles and runs without it.

---

## Install the SDK

The SDK ships as a `.unitypackage` you import into your Unity project.

1. Download `Viverse-Unity-SDK-<version>.unitypackage` from the VIVERSE releases page.
2. In Unity, open **Assets > Import Package > Custom Package** and select the file.
3. Leave every item checked in the import dialog and select **Import**.
4. Wait for Unity to finish recompiling. You should see zero errors in the Console.

The SDK lands under `Assets/viverse-unity-sdk/` and adds three assemblies to your project: `Viverse.SDK` (runtime), `Viverse.SDK.Editor` (editor tooling), and `Viverse.NativeWebSocket` (a vendored WebSocket transport).

> The vendored `Viverse.NativeWebSocket` assembly is renamed on purpose. If your project already uses the community `NativeWebSocket` package, both assemblies coexist without duplicate-type or duplicate-asmdef errors. See [Coexisting with an existing NativeWebSocket](#coexisting-with-an-existing-nativewebsocket) for details.

---

## Authenticate a player

Every feature except the public avatar catalog requires an access token. `AuthManager` is a singleton MonoBehaviour that runs the OAuth flow for you.

1. Create an empty GameObject in your first scene and name it `[AuthManager]`.
2. Add the `AuthManager` component to it.
3. In your bootstrap script, initialize the manager with your App ID and subscribe to the login callbacks.

```csharp
using UnityEngine;
using ViverseSDK;

public class Bootstrap : MonoBehaviour
{
    void Start()
    {
        var auth = AuthManager.Instance;
        auth.OnLoginSuccess += result => Debug.Log($"Signed in as {result.account_id}");
        auth.OnError        += err    => Debug.LogError($"Auth error: {err}");

        auth.Initialize("YOUR_APP_ID");
    }

    public void OnLoginButtonClicked()  => AuthManager.Instance.Login();
    public void OnLogoutButtonClicked() => AuthManager.Instance.Logout();
}
```

In WebGL, `Login()` redirects the parent page through the VIVERSE OAuth server. In the Editor, `AuthHttpServer` starts a temporary local server on port `40078` to receive the redirect. Both paths raise `OnLoginSuccess` with the same `AuthResult` payload.

---

## Invoke Lambda functions

`LambdaClient` calls server-side functions that you register in VIVERSE Studio. It returns a `LambdaResult` with the status, JSON payload, and error message.

```csharp
using ViverseSDK;

var lambda = new LambdaClient("YOUR_APP_ID");
string token = AuthManager.Instance.AccessToken;

var result = await lambda.Invoke(
    eventName: "give_daily_reward",
    dataJson:  "{\"day\":3}",
    token:     token
);

if (result.success) Debug.Log($"Reward: {result.result}");
else                Debug.LogError($"Lambda failed: {result.error}");
```

When you register the script in Studio, return values with `reply(...)`. `resolve(...)` returns `script_error` because the runtime does not treat it as a completion signal.

---

## Match players into rooms

`MatchmakingClient` is a singleton MonoBehaviour that manages a persistent WebSocket connection to the broadcasting gateway. Subscribe to events **before** you call `Initialize`, because the server sends `OnConnect` as soon as the socket opens.

```csharp
using UnityEngine;
using ViverseSDK;

var go     = new GameObject("[MatchmakingClient]");
var client = go.AddComponent<MatchmakingClient>();

client.OnConnect += () =>
{
    // Set actor info first — the server rejects room commands until you do.
    client.SendRequest("SetActor",
        "{\"properties\":{\"name\":\"Player1\"}}",
        response: null);
};

client.OnJoinRoom       += json => Debug.Log($"Joined room: {json}");
client.OnRoomListUpdate += json => Debug.Log($"Room list: {json}");

client.Initialize(appId: "YOUR_APP_ID", debugMode: true);
```

Once the actor is set, you can list, create, join, and leave rooms:

```csharp
client.SendRequest("GetAvailableRooms", "{}", response =>
{
    // Parse the response JSON and update your UI.
});

client.SendRequest("CreateRoom",
    "{\"room_name\":\"MyRoom\",\"max_players\":4,\"min_players\":2}",
    response: null);

client.SendRequest("JoinRoom",  "{\"room_id\":\"uuid-here\"}", null);
client.SendRequest("LeaveRoom", "{}", null);
```

---

## Sync players in real time

After a player joins a room, hand the `room_id` to `MultiplayerClient`. The client opens a WebRTC data channel through mediasoup and exposes five sub-modules for different sync patterns.

```csharp
using UnityEngine;
using ViverseSDK;

var go = new GameObject("[MultiplayerClient]");
var mp = go.AddComponent<MultiplayerClient>();

mp.OnConnected    += ()  => Debug.Log("Multiplayer connected");
mp.OnDisconnected += ()  => Debug.Log("Multiplayer disconnected");

string roomId    = /* from the MatchmakingClient JoinRoom response */;
string sessionId = System.Guid.NewGuid().ToString();

await mp.Initialize(roomId, appId: "YOUR_APP_ID", sessionId);

var options = new MultiplayerInitOptions
{
    modules = new ModulesConfig
    {
        game        = new ModuleOption { enabled = true, play_time = 60, total_player = 4 },
        networkSync = new ModuleOption { enabled = true },
        actionSync  = new ModuleOption { enabled = true },
        leaderboard = new ModuleOption { enabled = true },
    },
};

await mp.Init(options);
```

### Modules at a glance

Each module handles a specific sync pattern. Send data with synchronous method calls; receive data through `Action<string>` events that carry a JSON payload.

| Module | Purpose | Send | Receive |
|---|---|---|---|
| `Game` | Lifecycle and timing | `Ready()`, `TriggerGameStart()`, `TriggerGameEnd()`, `TriggerGameRestart()` | `OnMasterNotify`, `OnPlayerAllReady`, `OnCountdownToStart`, `OnCountdownToEnd`, `OnGameEnd`, `OnGameRestart` |
| `NetworkSync` | Continuous transform sync (every frame) | `UpdateMyPosition(json)`, `UpdateEntityPosition(entityId, json)` | `OnNotifyPositionUpdate`, `OnNotifyRemove` |
| `ActionSync` | Discrete action broadcasts (attacks, skills, emotes) | `Competition(name, msg, actionId)` | `OnCompetition` |
| `Leaderboard` | Real-time in-room scores | `LeaderboardUpdate(int score)` | `OnLeaderboardUpdate` |
| `General` | Freeform peer messaging | `SendMessage(json)` | `OnMessage`, `OnClientConnected`, `OnClientDisconnected` |

> `NetworkSync` is for **continuous** per-frame updates such as position and rotation. `ActionSync` is for **discrete** events that should not be repeated, and de-duplicates by `action_id`.

> The multiplayer `Leaderboard` module broadcasts scores live within a room over WebRTC and does not persist them. To store global rankings across sessions, use `LeaderboardClient` (see [Submit scores and read rankings](#submit-scores-and-read-rankings)).

---

## Save player progress to the cloud

`CloudSaveClient` supports two shapes: versioned save files (a full history of blobs) and key-value entries for small named values.

```csharp
using ViverseSDK;

var cloud = new CloudSaveClient("YOUR_APP_ID");
string token = AuthManager.Instance.AccessToken;

// Versioned saves
await cloud.Save("{\"level\":5,\"score\":1200}", token);
var latest = await cloud.GetLatest(token);
if (latest.success) Debug.Log($"Latest save: {latest.data}");

// Key-value entries
await cloud.SetPlayerData("coins", "150", token);
var coins = await cloud.GetPlayerData("coins", token);
if (coins.success) Debug.Log($"Coins: {coins.data}");
```

Every method returns a `CloudSaveResult` with `success`, `data`, and `error` fields. Write operations return `null` in `data`.

---

## Submit scores and read rankings

`LeaderboardClient` submits scores with RSA/AES-encrypted payloads and reads back rankings. You must create the leaderboard in VIVERSE Studio first, and the `metaName` argument must match the one you configured there.

```csharp
using ViverseSDK;

var leaderboard = new LeaderboardClient("YOUR_APP_ID");
string token = AuthManager.Instance.AccessToken;

await leaderboard.SubmitScore("time_attack", "100", token);

var mine   = await leaderboard.GetLeaderboard("time_attack", token);
var guest  = await leaderboard.GetGuestLeaderboard("time_attack");

if (mine.success)  Debug.Log($"My ranking: {mine.data}");
if (guest.success) Debug.Log($"Guest ranking: {guest.data}");
```

Score behavior (accumulate versus keep best) and sort order are set server-side in Studio, not through the client.

### WebGL leaderboard notes

Deployed WebGL builds run inside an iframe at `*.world.viverse.app`, which cannot proxy API traffic. The jslib detects the environment and switches URLs automatically:

- **Localhost**: relative URLs (`/`). The included `serve_webgl.sh` script proxies `/api/vrleaderboard/*`, `/api/ironhide/*`, and `/api/optimusprime/*` to `viveport.com`.
- **Deployed**: absolute URLs to `https://www.viveport.com/`. Rankings use viverse-sdk's `GameDashboard.getLeaderboard()` so that community display names stay current — the raw REST endpoint returns stale names captured at submission time.

---

## Fetch and unlock achievements

`AchievementsClient` mirrors the leaderboard flow. You register each achievement in Studio, and the `api_name` in your unlock payload must match exactly.

```csharp
using ViverseSDK;

var achievements = new AchievementsClient("YOUR_APP_ID");
string token = AuthManager.Instance.AccessToken;

// Read all achievements for the current player
var list = await achievements.GetUserAchievements(token);
if (list.success) Debug.Log($"Achievements: {list.data}");

// Unlock one or more achievements
string payload =
    "[{\"api_name\":\"first_win\",\"unlock\":true}," +
    " {\"api_name\":\"score_1000\",\"unlock\":true}]";

await achievements.UnlockAchievements(payload, token);
```

> Setting `unlock: false` is accepted by the API but has no effect. Achievements are one-way.

---

## Load a player's avatar

`AvatarClient` reads profile data, lists avatars, and downloads VRM binaries. Rendering a downloaded VRM requires UniVRM, which is optional (see [Optional UniVRM support](#optional-univrm-support)).

```csharp
using ViverseSDK;

var avatar = new AvatarClient();
string token = AuthManager.Instance.AccessToken;

var profile = await avatar.GetProfile(token);
var active  = await avatar.GetActiveAvatar(token);
if (!active.success) return;

string vrmUrl = /* extract VrmBinaryDataUrl from active.data */;
var download  = await avatar.DownloadAvatarFile(vrmUrl, token);

if (download.success && download.data != null)
{
    var controller = GetComponent<VrmAvatarController>();
    await controller.LoadVrmFromBytes(download.data);
}
```

For the public avatar catalog, use `GetPublicAvatarList()` and `GetPublicAvatarByID(id)`. These calls do not require an access token.

### WebGL avatar notes

In WebGL, VRM downloads follow a three-tier fallback: viverse-sdk's `Avatar.getAvatarFileWithSDK()`, then the Avatar SDK's `viaWorker({action:'downloadAndDecrypt'})`, and finally a plain fetch. The Avatar SDK global is `globalThis.newViveAvatarSdk` (a factory function), not `window.AvatarSDK`.

For MToon materials to render correctly, add the VRM shaders to **Project Settings > Graphics > Always Included Shaders**.

---

## Build for WebGL

The SDK ships a build post-processor at `Editor/ViverseSDKBuildProcessor.cs` that injects the viverse-sdk loader script into your generated `index.html` after every WebGL build. You do not need a custom WebGL template.

### How auto-injection works

1. You build the project for WebGL using Unity's **Default** template.
2. Unity runs the SDK's `[PostProcessBuild]` callback.
3. The callback opens `index.html` and inserts the following snippet immediately before Unity's own `<script src="Build/...">` loader:

   ```html
   <!-- viverse-unity-sdk:auto-injected -->
   <script src="https://www.viverse.com/static-assets/viverse-sdk/index.umd.cjs"></script>
   ```

4. The callback is idempotent. Rebuilds do not double-inject. If your custom template already contains a `viverse-sdk` script tag, the callback skips injection.

### Use your own template

If you maintain a custom WebGL template, you have two options:

- **Rely on auto-injection**: leave the viverse-sdk `<script>` tag out of your template. The build hook adds it for you.
- **Pin your own version**: include a `<script src="...">` with `viverse-sdk` in the URL. The hook detects it and skips injection.

---

## Test WebGL builds locally

The SDK ships `serve_webgl.sh` next to this guide's README. It serves a Unity WebGL build on `http://localhost:40078`, the redirect URI registered with the SDK's shared development OAuth client, and reverse-proxies VIVERSE backends so authenticated requests work from localhost without CORS errors.

```bash
# Make the script executable (first time only)
chmod +x Assets/viverse-unity-sdk/serve_webgl.sh

# Serve the default Build folder from your project root
./Assets/viverse-unity-sdk/serve_webgl.sh

# Serve a custom folder
./Assets/viverse-unity-sdk/serve_webgl.sh ./MyBuild

# Show all options
./Assets/viverse-unity-sdk/serve_webgl.sh -h
```

### Proxied endpoints

| Local request | Proxied to |
|---|---|
| `/api/vrleaderboard/*` | `https://www.viveport.com/api/vrleaderboard/*` |
| `/api/ironhide/*` | `https://www.viveport.com/api/ironhide/*` |
| `/api/optimusprime/*` | `https://www.viveport.com/api/optimusprime/*` |
| `/api/avatar/*` | `https://sdk-api.viverse.com/*` |
| `/api/avatar-files/*` | `https://avatar.viverse.com/*` |

**Requirements:** `bash`, `python3` version 3.7 or newer, and `lsof`.

**Why port 40078?** The shared development OAuth client only accepts `http://localhost:40078` as a redirect URI. To use a different port, register your own OAuth client in VIVERSE Studio and configure the SDK to use it. See `Runtime/AuthHttpServer.cs` and `Plugins/JSLib/ViverseAuth.jslib` for the integration points.

Deployed builds on VIVERSE hosting do not need the proxy. They run in an iframe under `*.world.viverse.app` and hit the absolute API URLs directly.

---

## Optional UniVRM support

The SDK compiles cleanly whether or not UniVRM is installed. VRM rendering is packaged as an optional sample assembly that Unity activates automatically when it detects the `com.vrmc.vrm` package.

- **Without UniVRM**: the SDK compiles without errors. `AvatarClient.DownloadAvatarFile()` still returns the raw VRM bytes. `VrmAvatarController` is unavailable, and the sample runner logs a startup warning that tells you how to enable rendering.
- **With UniVRM installed**: Unity picks up `com.vrmc.vrm` through a `versionDefines` entry in `Sample/Viverse.SDK.Sample.asmdef` and enables `VrmAvatarController` on the next recompile.

To install UniVRM, add both packages to `Packages/manifest.json`:

```json
"com.vrmc.gltf": "https://github.com/vrm-c/UniVRM.git?path=/Assets/UniGLTF#v0.130.1",
"com.vrmc.vrm":  "https://github.com/vrm-c/UniVRM.git?path=/Assets/VRM10#v0.130.1"
```

Unity recompiles automatically. `VrmAvatarController` activates on the next Editor refresh — no SDK reinstall required.

---

## Coexisting with an existing NativeWebSocket

The community `NativeWebSocket` package is a common Unity dependency, and importing two copies of the same assembly breaks the build. The SDK avoids the collision by vendoring its copy under a private namespace.

| | Community package | SDK's vendored copy |
|---|---|---|
| Assembly name | `NativeWebSocket` | `Viverse.NativeWebSocket` |
| Namespace | `NativeWebSocket` | `Viverse.NativeWebSocket` |
| jslib exports | `WebSocketConnect`, `WebSocketAllocate`, ... | `ViverseWebSocketConnect`, `ViverseWebSocketAllocate`, ... |

Unity treats the two assemblies as unrelated. Your application code keeps calling `NativeWebSocket.WebSocket`, and the SDK uses `Viverse.NativeWebSocket.WebSocket` internally. No user configuration is required.

The vendored copy adds roughly 30 KB to the final build. If you need true deduplication across the whole project, migrate to the Unity Package Manager (UPM), which resolves shared dependencies for you.

---

## API reference

### AuthManager

| Member | Kind | Description |
|---|---|---|
| `Instance` | static property | Returns the singleton instance. |
| `IsLoggedIn` | property | `true` when the current token is valid. |
| `AccessToken` | property | The current JWT access token. |
| `AccountId` | property | The signed-in player's account UUID. |
| `OnLoginSuccess` | event | Fires with an `AuthResult` on successful sign-in. |
| `OnLogout` | event | Fires on sign-out. |
| `OnError` | event | Fires with an error message string. |
| `Initialize(appId)` | method | Initializes the SDK with your App ID. |
| `Login()` | method | Starts the OAuth flow. |
| `Logout()` | method | Clears the session and tokens. |

### LambdaClient

| Member | Kind | Description |
|---|---|---|
| `LambdaClient(appId)` | constructor | Creates a client bound to your App ID. |
| `Invoke(eventName, dataJson, token, ct)` | async method | Invokes a Lambda function and returns a `LambdaResult`. |

**LambdaResult**

| Field | Type | Description |
|---|---|---|
| `success` | `bool` | `true` if the invocation succeeded. |
| `status` | `string` | `"succeeded"`, `"failed"`, or `"timeout"`. |
| `result` | `string` | The JSON payload returned from the function. |
| `error` | `string` | The error message on failure. |

### CloudSaveClient

| Member | Kind | Description |
|---|---|---|
| `CloudSaveClient(appId)` | constructor | Creates a client bound to your App ID. |
| `Save(dataJson, token)` | async method | Saves a new version of the player's data. |
| `GetAll(token)` | async method | Returns every saved version. |
| `GetLatest(token)` | async method | Returns the most recent version. |
| `Delete(version, token)` | async method | Deletes the specified version. |
| `SetPlayerData(key, dataJson, token)` | async method | Sets a key-value entry. |
| `GetPlayerData(key, token)` | async method | Reads the value stored at `key`. |

**CloudSaveResult**

| Field | Type | Description |
|---|---|---|
| `success` | `bool` | `true` if the operation succeeded. |
| `data` | `string` | The JSON response, or `null` for write operations. |
| `error` | `string` | The error message on failure. |

### LeaderboardClient

| Member | Kind | Description |
|---|---|---|
| `LeaderboardClient(appId)` | constructor | Creates a client bound to your App ID. |
| `GetLeaderboard(metaName, token, ...)` | async method | Reads rankings as an authenticated player, including your own rank. |
| `GetGuestLeaderboard(metaName, ...)` | async method | Reads rankings anonymously. |
| `SubmitScore(metaName, value, token)` | async method | Submits an encrypted score. |

### AchievementsClient

| Member | Kind | Description |
|---|---|---|
| `AchievementsClient(appId)` | constructor | Creates a client bound to your App ID. |
| `GetUserAchievements(token, ct)` | async method | Returns every achievement plus its unlock status. |
| `UnlockAchievements(achievementsJson, token, ct)` | async method | Unlocks one or more achievements. `achievementsJson` is a JSON array of `{"api_name":"x","unlock":true}` entries. |

### AvatarClient

| Member | Kind | Description |
|---|---|---|
| `AvatarClient()` | constructor | Creates a client. No App ID needed. |
| `GetProfile(token)` | async method | Returns the player's profile. |
| `GetAvatarList(token)` | async method | Returns the player's avatar list. |
| `GetActiveAvatar(token)` | async method | Returns the currently active avatar. |
| `GetPublicAvatarList()` | async method | Returns the public avatar catalog (no auth). |
| `GetPublicAvatarByID(avatarId)` | async method | Returns a public avatar by ID (no auth). |
| `DownloadAvatarFile(vrmUrl, token)` | async method | Downloads the VRM binary. |

**AvatarDownloadResult**

| Field | Type | Description |
|---|---|---|
| `success` | `bool` | `true` if the download succeeded. |
| `data` | `byte[]` | Raw VRM bytes. |
| `size` | `int` | Byte count. |
| `error` | `string` | The error message on failure. |

### VrmAvatarController

Available only when UniVRM is installed. See [Optional UniVRM support](#optional-univrm-support).

| Member | Kind | Description |
|---|---|---|
| `IsLoaded` | property | `true` when a VRM model is loaded. |
| `StatusText` | property | The current status string for UI display. |
| `OnVrmLoaded` | event | Fires when a VRM loads successfully. |
| `OnVrmLoadFailed` | event | Fires with an error string on load failure. |
| `LoadVrmFromBytes(vrmBytes)` | async method | Loads and spawns a VRM model. |
| `DestroyVrm()` | method | Destroys the current model. |

---

## Project layout

The SDK follows Unity's standard package layout so you can navigate it in the Project window.

```
Assets/viverse-unity-sdk/
├── Editor/                        # Editor-only tooling (build post-processor)
├── Runtime/                       # Core clients and multiplayer modules
│   ├── AuthManager.cs
│   ├── LambdaClient.cs
│   ├── CloudSaveClient.cs
│   ├── LeaderboardClient.cs
│   ├── AchievementsClient.cs
│   ├── AvatarClient.cs
│   ├── MatchmakingClient.cs
│   ├── MultiplayerClient.cs
│   └── Modules/                   # Game, NetworkSync, ActionSync, Leaderboard, General, Lambda
├── Plugins/
│   ├── Viverse.NativeWebSocket/   # Vendored WebSocket transport
│   └── JSLib/                     # WebGL bridges to viverse-sdk
├── Sample/
│   ├── ViverseTestRunner.cs       # Interactive test UI for every feature
│   └── VRM/                       # Compiles only when UniVRM is installed
├── README.md
├── serve_webgl.sh                 # Local WebGL server plus CORS proxy
└── export_sdk.sh                  # Rebuilds the .unitypackage
```

---

## Where to go next

- [Publish your build to VIVERSE](https://studio.viverse.com/) using the VIVERSE CLI.
- Explore the sample scenes under `Assets/viverse-unity-sdk/Sample/` to see every feature wired up end to end.
- Read `README.md` inside the SDK folder for the terse developer reference used during day-to-day work.
