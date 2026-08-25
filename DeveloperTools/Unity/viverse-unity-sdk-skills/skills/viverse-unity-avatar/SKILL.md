---
name: viverse-unity-avatar
description: VIVERSE Unity SDK Avatar — User profiles, avatar lists, VRM model download and 3D loading with dual-path architecture.
prerequisites: [Unity 2021.2+, Auth token (user APIs), UniVRM v0.130.1+ (VRM loading)]
tags: [unity, avatar, vrm, viverse-sdk, webgl, editor, rest, 3d-model, profile]
---

# VIVERSE Unity SDK — Avatar (Profile & VRM Loading)

Retrieve user avatar profiles and load VRM 3D models into Unity scenes. Supports both authenticated (user's avatars) and public (catalog) access.

## When To Use This Skill

Use when a Unity project needs:
- User profile retrieval (display name, avatar info)
- Avatar list browsing (user's personal avatars or public catalog)
- VRM model downloading and 3D rendering in-scene
- Character customization or avatar display

## Architecture Overview

Avatar provides **two layers**:

```
┌──────────────────────────────────────────────────────────────┐
│  AvatarClient.cs (thin REST interface, no VRM dependency)     │
│  ├── WebGL: jslib fetch + Avatar SDK (decrypt encrypted VRM) │
│  └── Editor: UnityWebRequest (plain HTTP)                     │
├──────────────────────────────────────────────────────────────┤
│  VrmAvatarController.cs (VRM loading, movement, expressions)  │
│  ├── Depends on UniVRM10 (com.vrmc.vrm)                      │
│  ├── WebGL: RuntimeOnlyNoThreadAwaitCaller                   │
│  └── Editor: RuntimeOnlyAwaitCaller                          │
└──────────────────────────────────────────────────────────────┘
         │
         ▼  REST endpoints
┌──────────────────────────────────────────────────────────────┐
│  Avatar API                                                   │
│  https://sdk-api.viverse.com                                  │
│  ├── /api/meetingareaselector/v2/newgenavatar/sdk/me  (auth) │
│  ├── /api/meetingareaselector/v1/newgenavatar/getavatarlist   │
│  ├── /items/publicAvatar                         (no auth)   │
│  └── /items/publicAvatar/{id}                    (no auth)   │
└──────────────────────────────────────────────────────────────┘
```

## Read Order

1. This file (API + workflow)
2. [patterns/dual-path-avatar.md](patterns/dual-path-avatar.md) — WebGL/Editor implementation
3. [patterns/vrm-loading.md](patterns/vrm-loading.md) — VRM binary loading with UniVRM10

## Prerequisites

1. Unity 2021.2+ with .NET 4.x runtime
2. Valid access token (for user-specific APIs; public APIs need no auth)
3. [UniVRM v0.130.1+](https://github.com/vrm-c/UniVRM) (only required for VRM 3D loading)

**UniVRM setup** — add to `Packages/manifest.json`:
```json
"com.vrmc.gltf": "https://github.com/vrm-c/UniVRM.git?path=/Assets/UniGLTF#v0.130.1",
"com.vrmc.vrm": "https://github.com/vrm-c/UniVRM.git?path=/Assets/VRM10#v0.130.1"
```

## SDK File Structure

```
Assets/viverse-unity-sdk/
├── Runtime/AvatarClient.cs                # Thin C# interface (REST, no VRM dep)
├── Plugins/JSLib/ViverseAvatar.jslib      # WebGL bridge (Avatar SDK download+decrypt)
└── Sample/
    ├── ViverseTestRunner.cs               # Test UI with Avatar section
    └── VrmAvatarController.cs             # VRM load + movement + expressions (UniVRM10)
```

## Critical Implementation Rules

### Separation of Concerns (MANDATORY)

AvatarClient handles REST only. VRM rendering lives in a SEPARATE MonoBehaviour:

```
AvatarClient.cs        → REST API + binary download (ZERO UniVRM dependency)
VrmAvatarController.cs → VRM loading + movement + expressions (OWNS UniVRM10 dependency)
```

This ensures builds don't break if UniVRM is not installed.

### Auth — Required vs. Optional

| Method | Auth Required? |
|--------|:-:|
| `GetProfile(token)` | YES |
| `GetAvatarList(token)` | YES |
| `GetActiveAvatar(token)` | YES |
| `DownloadAvatarFile(vrmUrl, token)` | Optional (encrypted VRMs need it) |
| `GetPublicAvatarList()` | NO |
| `GetPublicAvatarByID(avatarId)` | NO |

### Token Header Format

VIVERSE APIs use custom headers (NOT `Authorization: Bearer`):

```
AccessToken: <jwt_token>
```

### WebGL Encrypted VRM Handling

VRM files may be encrypted. The WebGL jslib uses a **3-tier download strategy** for maximum compatibility across deployment environments:

```
Tier 1: viverse-sdk Avatar.getAvatarFileWithSDK(vrmUrl)
        → Uses viverse-sdk's built-in Avatar class (handles auth + decrypt internally)
        
Tier 2: Avatar SDK viaWorker({action:'downloadAndDecrypt', params:{modelUrl}})
        → Direct Avatar SDK worker-based download + decryption
        
Tier 3: Plain fetch(vrmUrl) with AccessToken header
        → Fallback for unencrypted VRMs or when SDKs unavailable
```

**Avatar SDK initialization** — the correct global is `globalThis.newViveAvatarSdk` (NOT `window.AvatarSDK`):

```javascript
var avatarSdkUrl = 'https://www.viverse.com/static-assets/avatar-sdk/v1.1.1/';
var AvatarSDK = globalThis.newViveAvatarSdk(
    { workerMinimum: 1, workerMaximum: navigator.hardwareConcurrency || 4 },
    avatarSdkUrl
);
```

**viverse-sdk Avatar class** (primary path):

```javascript
var Avatar = window.viverse.Avatar;
var avatarInstance = new Avatar({ baseURL: 'https://sdk-api.viverse.com/', token: accessToken });
var arrayBuffer = await avatarInstance.getAvatarFileWithSDK(vrmUrl);
```

This 3-tier approach works across all environments: VIVERSE hosting (iframe), localhost proxy, and direct access.

### WebGL Binary Transfer (JS → C#)

VRM bytes stored in JS buffer → C# copies via pinned array:

```csharp
byte[] buffer = new byte[size];
GCHandle handle = GCHandle.Alloc(buffer, GCHandleType.Pinned);
try {
    int copied = ViverseAvatar_CopyDownloadedBytes(bridgeId, handle.AddrOfPinnedObject(), size);
} finally {
    handle.Free();  // MUST free to prevent memory leak
}
```

### WebGL VRM Loading — No Threads

```csharp
#if UNITY_WEBGL && !UNITY_EDITOR
var awaitCaller = new RuntimeOnlyNoThreadAwaitCaller();  // REQUIRED
#else
IAwaitCaller awaitCaller = new RuntimeOnlyAwaitCaller();
#endif

_vrmInstance = await Vrm10.LoadBytesAsync(vrmBytes, canLoadVrm0X: true, showMeshes: true, awaitCaller: awaitCaller);
```

Using the default caller in WebGL **will deadlock**.

### glTF Header Validation

Before VRM loading, check the magic bytes:

```csharp
bool isGltf = vrmBytes.Length >= 4 &&
              vrmBytes[0] == 0x67 && vrmBytes[1] == 0x6C &&
              vrmBytes[2] == 0x54 && vrmBytes[3] == 0x46;
```

If invalid, the file is likely still encrypted (Avatar SDK decryption not available in Editor).

## Public API

```csharp
namespace ViverseSDK
{
    public class AvatarClient
    {
        public AvatarClient();  // No App ID needed

        // --- Authenticated (user's avatars) ---

        /// <summary>Get user profile (account info, display name).</summary>
        public async Task<AvatarResult> GetProfile(string token, CancellationToken ct = default);

        /// <summary>Get user's avatar list (includes VRM URLs, active avatar ID).</summary>
        public async Task<AvatarResult> GetAvatarList(string token, CancellationToken ct = default);

        /// <summary>Get the currently active avatar (helper: calls GetAvatarList + filters by CurrentAvatarId).</summary>
        public async Task<AvatarResult> GetActiveAvatar(string token, CancellationToken ct = default);

        // --- Public (no auth) ---

        /// <summary>Get public avatar catalog.</summary>
        public async Task<AvatarResult> GetPublicAvatarList(CancellationToken ct = default);

        /// <summary>Get specific public avatar by ID.</summary>
        public async Task<AvatarResult> GetPublicAvatarByID(string avatarId, CancellationToken ct = default);

        // --- Download ---

        /// <summary>Download VRM binary. WebGL decrypts via Avatar SDK; Editor does plain HTTP.</summary>
        public async Task<AvatarDownloadResult> DownloadAvatarFile(string vrmUrl, string token = null, CancellationToken ct = default);
    }

    [Serializable]
    public class AvatarResult
    {
        public bool success;
        public string data;   // JSON string
        public string error;  // Error message if failed
    }

    [Serializable]
    public class AvatarDownloadResult
    {
        public bool success;
        public byte[] data;   // Raw VRM bytes
        public int size;      // Byte count
        public string error;  // Error message if failed
    }
}
```

### VrmAvatarController (separate MonoBehaviour)

```csharp
public class VrmAvatarController : MonoBehaviour
{
    public bool IsLoaded { get; }
    public string StatusText { get; }
    public event Action OnVrmLoaded;
    public event Action<string> OnVrmLoadFailed;

    /// <summary>Load VRM from raw bytes. Destroys previous model.</summary>
    public async Task<bool> LoadVrmFromBytes(byte[] vrmBytes);

    /// <summary>Destroy current VRM model.</summary>
    public void DestroyVrm();

    // Built-in controls: WASD = move, 1-5 = expressions (Happy/Angry/Sad/Surprised/Relaxed), 0 = reset
}
```

## Integration Example

```csharp
using ViverseSDK;
using UnityEngine;

public class AvatarDemo : MonoBehaviour
{
    private AvatarClient _avatar;
    private VrmAvatarController _vrmController;

    void Start()
    {
        _avatar = new AvatarClient();
        _vrmController = GetComponent<VrmAvatarController>();
        _vrmController.OnVrmLoaded += () => Debug.Log("Avatar loaded in scene!");
        _vrmController.OnVrmLoadFailed += (err) => Debug.LogError($"VRM failed: {err}");
    }

    // Get user profile
    async void ShowProfile()
    {
        string token = AuthManager.Instance.AccessToken;
        var result = await _avatar.GetProfile(token);
        if (result.success) Debug.Log($"Profile: {result.data}");
    }

    // Download and display active avatar
    async void LoadActiveAvatar()
    {
        string token = AuthManager.Instance.AccessToken;

        // 1. Get active avatar metadata
        var active = await _avatar.GetActiveAvatar(token);
        if (!active.success || active.data == null) { Debug.Log("No active avatar"); return; }

        // 2. Extract VRM URL from avatar data
        var avatarData = MiniJson.Deserialize(active.data) as Dictionary<string, object>;
        string vrmUrl = avatarData?.ContainsKey("VrmBinaryDataUrl") == true
            ? avatarData["VrmBinaryDataUrl"]?.ToString()
            : avatarData?.ContainsKey("vrmUrl") == true
                ? avatarData["vrmUrl"]?.ToString()
                : null;

        if (string.IsNullOrEmpty(vrmUrl)) { Debug.LogError("No VRM URL found"); return; }

        // 3. Download VRM binary
        var dlResult = await _avatar.DownloadAvatarFile(vrmUrl, token);
        if (!dlResult.success) { Debug.LogError($"Download failed: {dlResult.error}"); return; }

        // 4. Load into scene
        await _vrmController.LoadVrmFromBytes(dlResult.data);
    }

    // Browse public avatars (no auth needed)
    async void ShowPublicAvatars()
    {
        var result = await _avatar.GetPublicAvatarList();
        if (result.success) Debug.Log($"Public avatars: {result.data}");
    }
}
```

## REST Endpoints

| Operation | Method | URL | Auth |
|-----------|--------|-----|:----:|
| GetProfile | GET | `/api/meetingareaselector/v2/newgenavatar/sdk/me` | YES |
| GetAvatarList | GET | `/api/meetingareaselector/v1/newgenavatar/getavatarlist` | YES |
| GetPublicAvatarList | GET | `/items/publicAvatar` | NO |
| GetPublicAvatarByID | GET | `/items/publicAvatar/{id}` | NO |
| DownloadAvatarFile | GET | (full URL from avatar data) | Optional |

Base URL: `https://sdk-api.viverse.com`

## Avatar List Response Structure

```json
{
  "version": "...",
  "data": {
    "CurrentAvatarId": 12345,
    "Avatars": [
      {
        "id": "12345",
        "Name": "My Avatar",
        "VrmBinaryDataUrl": "https://avatar.viverse.com/..../avatar.vrm",
        "vrmUrl": "https://...",
        "file": "https://...",
        "thumbnailUrl": "https://..."
      }
    ]
  }
}
```

**Finding VRM URL** — check fields in priority order: `VrmBinaryDataUrl` > `vrmUrl` > `file`

## WebGL Local Testing

Avatar API routes require proxy for localhost. Add to `serve_webgl.sh`:

```
/api/avatar/*  → https://sdk-api.viverse.com/*
/api/avatar-files/* → https://avatar.viverse.com/*
```

## WebGL Shader Setup

For proper VRM materials (MToon), add to **Project Settings > Graphics > Always Included Shaders**:
- `VRM/MToon10`
- `VRM10/Universal`
- `UniGLTF/UniUnlit`

Without this, materials fall back to Standard shader (pink/missing appearance).

## Compliance Gates

Before marking Avatar implementation complete:
- [ ] Editor: GetProfile returns user data with valid token
- [ ] Editor: GetAvatarList returns avatar array
- [ ] Editor: GetActiveAvatar extracts correct avatar by CurrentAvatarId
- [ ] Editor: DownloadAvatarFile returns valid glTF bytes
- [ ] Editor: VrmAvatarController loads VRM into scene
- [ ] WebGL: All profile/list APIs return data
- [ ] WebGL: VRM download + decrypt succeeds (Avatar SDK loaded)
- [ ] WebGL: Binary transfer (JS→C#) produces valid glTF bytes
- [ ] WebGL: VRM renders with correct materials (MToon shaders included)
- [ ] Public APIs work without auth token
- [ ] Expired token returns clear error (not crash)
- [ ] No UniVRM dependency in AvatarClient.cs
