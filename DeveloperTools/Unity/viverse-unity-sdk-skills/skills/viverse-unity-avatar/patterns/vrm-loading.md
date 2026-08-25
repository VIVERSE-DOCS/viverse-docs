# VRM Loading Pattern

## Overview

VRM loading is separated into `VrmAvatarController.cs` — a standalone MonoBehaviour that depends on UniVRM10. This keeps `AvatarClient.cs` clean of 3D rendering dependencies.

## Architecture

```
AvatarClient                    VrmAvatarController
(REST + download)               (3D rendering)
     │                               │
     │  DownloadAvatarFile()          │
     │  → returns byte[]             │
     │                               │
     └───── byte[] ─────────────────►│  LoadVrmFromBytes()
                                     │  → validates glTF header
                                     │  → Vrm10.LoadBytesAsync()
                                     │  → spawns GameObject
                                     │  → fires OnVrmLoaded
```

## VrmAvatarController API

```csharp
public class VrmAvatarController : MonoBehaviour
{
    [Header("Movement")]
    [SerializeField] private float moveSpeed = 2f;

    // Events
    public event Action OnVrmLoaded;
    public event Action<string> OnVrmLoadFailed;

    // State
    public bool IsLoaded { get; }       // Whether a model is active
    public string StatusText { get; }   // Current status for UI

    // Methods
    public async Task<bool> LoadVrmFromBytes(byte[] vrmBytes);
    public void DestroyVrm();
}
```

## Loading Flow

### 1. Validate glTF Header

```csharp
bool isGltf = vrmBytes.Length >= 4 &&
              vrmBytes[0] == 0x67 && vrmBytes[1] == 0x6C &&
              vrmBytes[2] == 0x54 && vrmBytes[3] == 0x46;

if (!isGltf)
{
    StatusText = "Invalid VRM: not a glTF file (possibly encrypted)";
    OnVrmLoadFailed?.Invoke(StatusText);
    return false;
}
```

### 2. Select Await Caller (Platform-Specific)

```csharp
#if UNITY_WEBGL && !UNITY_EDITOR
// WebGL is single-threaded — default caller deadlocks
var awaitCaller = new RuntimeOnlyNoThreadAwaitCaller();
#else
// Editor/Standalone can use threaded variant
IAwaitCaller awaitCaller = new RuntimeOnlyAwaitCaller();
#endif
```

**Why this matters:** UniVRM's `LoadBytesAsync` internally uses task scheduling. In WebGL's single-threaded environment, the default `RuntimeOnlyAwaitCaller` waits on threads that never execute, causing a deadlock. `RuntimeOnlyNoThreadAwaitCaller` processes everything on the main thread.

### 3. Load VRM

```csharp
_vrmInstance = await Vrm10.LoadBytesAsync(
    vrmBytes,
    canLoadVrm0X: true,    // Support both VRM 0.x and 1.0
    showMeshes: true,       // Make renderers visible immediately
    awaitCaller: awaitCaller
);
```

### 4. Post-Load Setup

```csharp
_vrmInstance.transform.position = Vector3.zero;
_vrmInstance.transform.rotation = Quaternion.identity;
_vrmInstance.gameObject.name = "ViverseAvatar";

// Diagnostic logging
var renderers = _vrmInstance.GetComponentsInChildren<Renderer>();
Debug.Log($"VRM spawned: renderers={renderers.Length}");
foreach (var r in renderers)
    Debug.Log($"  {r.name}: shader={r.sharedMaterial?.shader?.name}");
```

## Built-In Controls

VrmAvatarController includes basic input handling for testing:

| Input | Action |
|-------|--------|
| W/A/S/D | Move character (normalized, with speed) |
| 1 | Expression: Happy |
| 2 | Expression: Angry |
| 3 | Expression: Sad |
| 4 | Expression: Surprised |
| 5 | Expression: Relaxed |
| 0 | Reset all expressions |

## Expressions Implementation

```csharp
private static readonly ExpressionKey[] Expressions = new[]
{
    ExpressionKey.Happy,
    ExpressionKey.Angry,
    ExpressionKey.Sad,
    ExpressionKey.Surprised,
    ExpressionKey.Relaxed
};

// Set expression (reset all first, then apply one)
var expr = _vrmInstance.Runtime.Expression;
foreach (var key in Expressions)
    expr.SetWeight(key, 0f);
expr.SetWeight(Expressions[index], 1f);
```

## WebGL Shader Requirements

VRM models use MToon shaders. Unity WebGL builds strip unused shaders by default.

**Fix:** Add to **Project Settings > Graphics > Always Included Shaders**:
- `VRM/MToon10`
- `VRM10/Universal`
- `UniGLTF/UniUnlit`

**Symptoms of missing shaders:**
- Pink/magenta materials on avatar
- `Shader "VRM/MToon10" not found` in console
- Materials fall back to Standard shader (incorrect appearance)

## Encrypted VRM Handling

| Platform | Encrypted VRM | Unencrypted VRM |
|----------|:---:|:---:|
| WebGL | Avatar SDK `downloadAndDecrypt()` | Plain `fetch()` |
| Editor | Not supported (returns invalid bytes) | `UnityWebRequest` download |

**Editor limitation:** If avatar uses encrypted VRM, the Editor path will download encrypted bytes that fail glTF validation. This is expected — encrypted VRMs only fully work in WebGL where Avatar SDK is available.

## Cleanup

```csharp
public void DestroyVrm()
{
    if (_vrmInstance != null)
    {
        Destroy(_vrmInstance.gameObject);
        _vrmInstance = null;
        _activeExpression = -1;
        StatusText = "";
    }
}
```

Always destroy previous model before loading a new one. `LoadVrmFromBytes` does this automatically.

## Integration with AvatarClient

Complete flow from API to rendered model:

```csharp
// 1. Get avatar metadata
var active = await _avatar.GetActiveAvatar(token);
var data = MiniJson.Deserialize(active.data) as Dictionary<string, object>;

// 2. Extract VRM URL (check multiple fields)
string vrmUrl = data?.ContainsKey("VrmBinaryDataUrl") == true
    ? data["VrmBinaryDataUrl"]?.ToString()
    : data?.ContainsKey("vrmUrl") == true
        ? data["vrmUrl"]?.ToString()
        : data?.ContainsKey("file") == true
            ? data["file"]?.ToString()
            : null;

// 3. Download binary
var dlResult = await _avatar.DownloadAvatarFile(vrmUrl, token);

// 4. Load into scene
var controller = GetComponent<VrmAvatarController>();
bool success = await controller.LoadVrmFromBytes(dlResult.data);
```

## Gotchas

1. **UniVRM not installed** — `VrmAvatarController.cs` won't compile. That's OK — `AvatarClient.cs` still works for REST-only use cases (profile display, list browsing).

2. **Multiple LoadVrmFromBytes calls** — Each call destroys the previous model. No need to manually call `DestroyVrm()` first.

3. **WebGL build size** — UniVRM adds ~2-5MB to WebGL build. Consider if VRM loading is needed vs. just REST profile display.

4. **VRM 0.x vs 1.0** — `canLoadVrm0X: true` enables compatibility with older VRM files. Most VIVERSE avatars are VRM 1.0 but some older ones may be 0.x.
