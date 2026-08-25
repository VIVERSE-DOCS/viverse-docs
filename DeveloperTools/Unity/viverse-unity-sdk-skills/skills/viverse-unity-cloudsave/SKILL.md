---
name: viverse-unity-cloudsave
description: VIVERSE Unity SDK Cloud Save — Persistent player data storage via REST API with dual-path architecture.
prerequisites: [Unity 2021.2+, VIVERSE App ID, Auth token]
tags: [unity, cloud-save, persistence, viverse-sdk, webgl, editor, rest, storage-sdk]
---

# VIVERSE Unity SDK — Cloud Save (Persistent Storage)

Save and load player data from Unity. Pure REST — no WebRTC or multiplayer connection required.

## When To Use This Skill

Use when a Unity project needs:
- Persistent game saves (level progress, inventory, settings)
- Key-value player data (coins, scores, preferences)
- Cross-session data that survives between play sessions

## Architecture Overview

Cloud Save provides **two storage patterns**:

```
┌──────────────────────────────────────────────────────────────┐
│  CloudSaveClient.cs (thin interface)                          │
│  ├── WebGL: jslib fetch() → BCGW REST API                    │
│  └── Editor: UnityWebRequest → BCGW REST API                 │
├──────────────────────────────────────────────────────────────┤
│  Two Storage Patterns:                                        │
│  ├── UserApp (versioned): save/getAll/getLatest/delete        │
│  └── PlayerData (key-value): setPlayerData/getPlayerData      │
└──────────────────────────────────────────────────────────────┘
         │
         ▼  REST endpoints
┌──────────────────────────────────────────────────────────────┐
│  Broadcasting Gateway (BCGW)                                  │
│  https://broadcasting-gateway-gaming.vrprod.viveport.com      │
│  ├── /api/webrtcbot-service/v1/userapp/...                   │
│  └── /api/webrtcbot-service/v1/cloudsave/...                 │
└──────────────────────────────────────────────────────────────┘
```

## Read Order

1. This file (API + workflow)
2. [patterns/dual-path-cloudsave.md](patterns/dual-path-cloudsave.md)

## Prerequisites

1. Unity 2021.2+ with .NET 4.x runtime
2. App ID from [VIVERSE Studio](https://studio.viverse.com/)
3. Valid access token (user must be logged in via AuthManager)

## SDK File Structure

```
Assets/viverse-unity-sdk/
├── Runtime/CloudSaveClient.cs              # Thin C# interface (standalone, async)
├── Plugins/JSLib/ViverseCloudSave.jslib   # WebGL bridge (fetch-based)
└── Sample/ViverseTestRunner.cs            # Test UI with Cloud Save section
```

## Critical Implementation Rules

### Auth Is Required First

Cloud Save requires a valid (non-expired) access token:

```csharp
if (!AuthManager.Instance.IsLoggedIn)
{
    Debug.LogError("Must login before using Cloud Save");
    return;
}
```

### Token Header Format

VIVERSE APIs use custom headers (NOT `Authorization: Bearer`):

```
JWT token (3 dot-separated segments) → AccessToken: <jwt>
Non-JWT (AuthKey)                    → AuthKey: <key>
```

### setPlayerData Body Must Be JSON Object

The server rejects bare primitives. Always wrap non-object values:

```csharp
// WRONG: Server returns 400 "Invalid request body"
await cloudSave.SetPlayerData("coins", "500", token);

// CORRECT: Wrapped as JSON object
await cloudSave.SetPlayerData("coins", "{\"value\": 500}", token);
```

The SDK auto-wraps primitives as `{"value": x}` — users can pass bare values safely.

### GetPlayerData Response Structure

Server returns `{"data": {"key": value}}` — NOT nested under `player_data`:

```json
{
  "id": "...",
  "user_id": "...",
  "app_id": "5tn6tdp85c",
  "data": {"coins": {"value": 500}},
  "created_at": "...",
  "updated_at": "..."
}
```

### Delete Version — String in WebGL

C# `long` causes pointer corruption in Emscripten. The version parameter is passed as `string` to jslib.

### No Encryption Needed

Cloud Save uses plain JSON over HTTPS with AccessToken header. No RSA/AES encryption required (unlike the old viverse-unity-sdk reference).

## Public API

```csharp
namespace ViverseSDK
{
    public class CloudSaveClient
    {
        public CloudSaveClient(string appId);

        // --- Versioned Saves (UserApp) ---

        /// <summary>Save game data (creates a new version).</summary>
        public async Task<CloudSaveResult> Save(string dataJson, string token);

        /// <summary>Get all saved versions.</summary>
        public async Task<CloudSaveResult> GetAll(string token);

        /// <summary>Get the most recent save.</summary>
        public async Task<CloudSaveResult> GetLatest(string token);

        /// <summary>Delete a specific version.</summary>
        public async Task<CloudSaveResult> Delete(long version, string token);

        // --- Key-Value Storage (PlayerData) ---

        /// <summary>Set a key-value pair. Body is auto-wrapped if primitive.</summary>
        public async Task<CloudSaveResult> SetPlayerData(string key, string dataJson, string token);

        /// <summary>Get player data by key.</summary>
        public async Task<CloudSaveResult> GetPlayerData(string key, string token);
    }

    [Serializable]
    public class CloudSaveResult
    {
        public bool success;
        public string data;   // JSON string (null for write operations)
        public string error;  // Error message if failed
    }
}
```

## Integration Example

```csharp
using ViverseSDK;
using UnityEngine;

public class MyGame : MonoBehaviour
{
    private CloudSaveClient _cloudSave;

    void Start()
    {
        _cloudSave = new CloudSaveClient("YOUR_APP_ID");
    }

    // --- Versioned Saves ---

    async void SaveGame()
    {
        string token = AuthManager.Instance.AccessToken;
        var result = await _cloudSave.Save("{\"level\": 5, \"score\": 1200}", token);
        if (result.success) Debug.Log("Game saved!");
    }

    async void LoadLatest()
    {
        string token = AuthManager.Instance.AccessToken;
        var result = await _cloudSave.GetLatest(token);
        if (result.success) Debug.Log($"Save data: {result.data}");
        // result.data = {"user_id":"...","app_id":"...","data":{"level":5,"score":1200},"version":123456}
    }

    // --- Key-Value Storage ---

    async void SetCoins(int amount)
    {
        string token = AuthManager.Instance.AccessToken;
        var result = await _cloudSave.SetPlayerData("coins", amount.ToString(), token);
        // Primitive auto-wrapped as {"value": amount}
    }

    async void GetCoins()
    {
        string token = AuthManager.Instance.AccessToken;
        var result = await _cloudSave.GetPlayerData("coins", token);
        if (result.success && result.data != null)
            Debug.Log($"Coins data: {result.data}"); // {"value": 500}
    }
}
```

## REST Endpoints

| Operation | Method | URL | Body |
|-----------|--------|-----|------|
| Save | POST | `/api/webrtcbot-service/v1/userapp/save` | `{"app_id":"...","data":{...}}` |
| GetAll | GET | `/api/webrtcbot-service/v1/userapp/{appId}/all` | — |
| GetLatest | GET | `/api/webrtcbot-service/v1/userapp/{appId}/latest` | — |
| Delete | DELETE | `/api/webrtcbot-service/v1/userapp/{appId}/version/{version}` | — |
| SetPlayerData | POST | `/api/webrtcbot-service/v1/cloudsave/{appId}/upsert/{key}` | JSON object |
| GetPlayerData | GET | `/api/webrtcbot-service/v1/cloudsave/{appId}` | — |

Base URL: `https://broadcasting-gateway-gaming.vrprod.viveport.com`

## WebGL Callback Bridge

WebGL path uses `SendMessage` to bridge jslib → C#:

1. `CloudSaveClient.Method()` → registers `TaskCompletionSource` in `CloudSaveCallbackRegistry`
2. jslib function → fetch → `SendMessage(gameObject, "OnCloudSaveCallback", json)`
3. `AuthManager.OnCloudSaveCallback()` → extracts `bridgeId` → resolves the TCS
4. `CloudSaveClient` awaits TCS → returns `CloudSaveResult`

## Compliance Gates

Before marking Cloud Save implementation complete:
- [ ] Editor: Login → Save → GetLatest shows saved data
- [ ] Editor: GetAll returns array of versions
- [ ] Editor: DeleteLatest → GetLatest shows older version
- [ ] Editor: SetPlayerData → GetPlayerData returns stored value
- [ ] WebGL: All 6 operations succeed
- [ ] Primitives auto-wrapped (no 400 errors)
- [ ] Expired token returns clear error
- [ ] No dependency on MultiplayerClient
