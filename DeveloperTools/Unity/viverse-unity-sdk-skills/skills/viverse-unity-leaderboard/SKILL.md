---
name: viverse-unity-leaderboard
description: VIVERSE Unity SDK Leaderboard — Score submission and ranking retrieval via REST with RSA/AES encryption, dual-path architecture.
prerequisites: [Unity 2021.2+, VIVERSE App ID, Auth token, Leaderboard configured in Studio]
tags: [unity, leaderboard, scores, ranking, viverse-sdk, webgl, editor, rest, encryption]
---

# VIVERSE Unity SDK — Leaderboard (Score Submission & Rankings)

Submit scores and retrieve rankings from Unity. REST-based with RSA/AES encryption for score integrity.

## When To Use This Skill

Use when a Unity project needs:
- Score submission to a global leaderboard
- Ranking retrieval (authenticated or guest)
- Encrypted score submission (tamper-proof)

## Architecture Overview

```
┌──────────────────────────────────────────────────────────────┐
│  LeaderboardClient.cs (thin interface)                        │
│  ├── WebGL: jslib → viverse-sdk GameDashboard (encryption)   │
│  │          jslib → fetch (rankings, relative URLs + proxy)  │
│  └── Editor: UnityWebRequest + RSA/AES (Ironhide flow)       │
├──────────────────────────────────────────────────────────────┤
│  Three Operations:                                            │
│  ├── GetLeaderboard (authenticated ranking)                   │
│  ├── GetGuestLeaderboard (no auth required)                   │
│  └── SubmitScore (encrypted via RSA + AES)                    │
└──────────────────────────────────────────────────────────────┘
         │
         ▼  REST endpoints
┌──────────────────────────────────────────────────────────────┐
│  viveport.com                                                 │
│  ├── /api/vrleaderboard/v1/apps/{appId}/metas/ranking        │
│  ├── /api/vrleaderboard/v1/apps/{appId}/metas/guest_ranking  │
│  ├── /api/vrleaderboard/v1/apps/{appId} (POST scores)        │
│  └── /api/ironhide/v1/token (session + key exchange)         │
└──────────────────────────────────────────────────────────────┘
```

## Read Order

1. This file (API + workflow)
2. [patterns/dual-path-leaderboard.md](patterns/dual-path-leaderboard.md)

## Prerequisites

1. Unity 2021.2+ with .NET 4.x runtime
2. App ID from [VIVERSE Studio](https://studio.viverse.com/)
3. Valid access token (user must be logged in via AuthManager)
4. **Leaderboard must be created in VIVERSE Studio** with a `metaName`

## SDK File Structure

```
Assets/viverse-unity-sdk/
├── Runtime/LeaderboardClient.cs            # Thin C# interface (standalone, async)
├── Plugins/JSLib/ViverseLeaderboard.jslib  # WebGL bridge (fetch + GameDashboard)
└── Sample/ViverseTestRunner.cs             # Test UI with Leaderboard section
```

## Critical Implementation Rules

### Leaderboard Must Exist in Studio

You MUST create the leaderboard in VIVERSE Studio before API calls work:
- `metaName` is case-sensitive and must match exactly
- `sort_type` controls ranking order (server-side)
- `update_type` controls score behavior: accumulate (1) or best (2)

### Auth Required for Submit and Ranking

```csharp
if (!AuthManager.Instance.IsLoggedIn)
{
    Debug.LogError("Must login before submitting scores or getting authenticated ranking");
    return;
}
// GetGuestLeaderboard does NOT require auth
```

### Token Header Format

VIVERSE APIs use custom headers (NOT `Authorization: Bearer`):

```
JWT token (3 dot-separated segments) → AccessToken: <jwt>
Non-JWT (AuthKey)                    → AuthKey: <key>
```

### CORS & URL Handling (WebGL)

`viveport.com` does NOT allow CORS for localhost origins. The WebGL jslib uses **environment-aware base URLs**:

```javascript
var baseUrl = (function() {
    if (window.location.hostname === 'localhost' || window.location.hostname === '127.0.0.1') {
        return '/';  // Use proxy (serve_webgl.sh)
    }
    return 'https://www.viveport.com/';  // Direct access from deployed builds
})();
```

- **Local testing**: `serve_webgl.sh` proxies `/api/vrleaderboard/*` and `/api/ironhide/*` to viveport.com
- **Deployed (VIVERSE hosting)**: Uses absolute URL `https://www.viveport.com/` — the iframe origin (`*.world.viverse.app`) cannot proxy, so direct CORS-allowed requests are required
- **Editor**: Uses absolute `https://www.viveport.com` in C# UnityWebRequest (no CORS restriction)

### Community Display Names (GetRanking / GetGuestRanking)

The direct REST API (`/api/vrleaderboard/...`) returns **stale display names** from score submission time. To show current community names, WebGL rankings use **viverse-sdk GameDashboard**:

```javascript
var GameDashboard = window.viverse.GameDashboard;
var gd = new GameDashboard({ baseURL: baseUrl, communityBaseURL: 'https://www.viverse.com/' });
var result = await gd.getLeaderboard(appId, { name: metaName, ... }, token);
```

`GameDashboard.getLeaderboard()` internally calls `_getCommunityDisplayName()` to map account IDs to current display names. This is why GetRanking/GetGuestRanking use viverse-sdk GameDashboard instead of raw REST — it resolves the name mismatch issue automatically.

### WebGL SubmitScore — Use GameDashboard

WebGL MUST use `viverse-sdk GameDashboard.uploadLeaderboardScore()` which bundles jsencrypt internally. Do NOT attempt Web Crypto PKCS1v1.5 — browsers don't support it for decrypt.

```javascript
// In jslib:
var gd = new window.viverse.GameDashboard({ baseURL: window.location.origin + '/' });
gd.uploadLeaderboardScore(appId, scores, token);
```

### Editor Encryption Flow (Ironhide)

1. Generate RSA 2048 keypair
2. Export public key as X509 DER Base64 (no PEM headers)
3. `GET /api/ironhide/v1/token?app_id={appId}&skip_ua=true` with `x-htc-public-key` header
4. Receive: `{ token: "session_token", key: "base64_encrypted_aes_key" }`
5. Decrypt `key` with RSA private key (PKCS1 padding)
6. Encrypt scores JSON with AES-CBC using decrypted symmetric key
7. `POST /api/vrleaderboard/v1/apps/{appId}` with body `{ scores: "<encrypted>" }` and headers `AccessToken` + `Token` (session)

## Public API

```csharp
namespace ViverseSDK
{
    public class LeaderboardClient
    {
        public LeaderboardClient(string appId);

        /// <summary>Get leaderboard ranking (requires authentication).</summary>
        public async Task<LeaderboardResult> GetLeaderboard(string metaName, string token,
            int rangeStart = 0, int rangeEnd = 100, string region = "global",
            string timeRange = "alltime", bool aroundUser = false, CancellationToken ct = default);

        /// <summary>Get guest leaderboard ranking (no authentication required).</summary>
        public async Task<LeaderboardResult> GetGuestLeaderboard(string metaName,
            int rangeStart = 0, int rangeEnd = 100, string region = "global",
            string timeRange = "alltime", string countryCode = "US", CancellationToken ct = default);

        /// <summary>Submit score with encryption (RSA + AES via Ironhide).</summary>
        public async Task<LeaderboardResult> SubmitScore(string metaName, string value, string token,
            CancellationToken ct = default);
    }

    [Serializable]
    public class LeaderboardResult
    {
        public bool success;
        public string data;   // JSON rankings (null for submit)
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
    private LeaderboardClient _leaderboard;

    void Start()
    {
        _leaderboard = new LeaderboardClient("YOUR_APP_ID");
    }

    // Submit a score (encrypted)
    async void SubmitScore(int score)
    {
        string token = AuthManager.Instance.AccessToken;
        var result = await _leaderboard.SubmitScore("highScore", score.ToString(), token);
        if (result.success) Debug.Log("Score submitted!");
        else Debug.LogError($"Submit failed: {result.error}");
    }

    // Get rankings (shows user's own rank)
    async void GetRanking()
    {
        string token = AuthManager.Instance.AccessToken;
        var result = await _leaderboard.GetLeaderboard("highScore", token, rangeEnd: 10);
        if (result.success) Debug.Log($"Top 10: {result.data}");
    }

    // Get rankings without login
    async void GetGuestRanking()
    {
        var result = await _leaderboard.GetGuestLeaderboard("highScore", rangeEnd: 10, countryCode: "US");
        if (result.success) Debug.Log($"Guest top 10: {result.data}");
    }
}
```

## REST Endpoints

| Operation | Method | URL | Auth |
|-----------|--------|-----|------|
| GetLeaderboard | GET | `/api/vrleaderboard/v1/apps/{appId}/metas/ranking?name={metaName}&...` | AccessToken |
| GetGuestLeaderboard | GET | `/api/vrleaderboard/v1/apps/{appId}/metas/guest_ranking?name={metaName}&...` | None |
| SubmitScore | POST | `/api/vrleaderboard/v1/apps/{appId}` | AccessToken + Token (session) |
| Ironhide | GET | `/api/ironhide/v1/token?app_id={appId}&skip_ua=true` | AccessToken + x-htc-public-key |

Base URL: `https://www.viveport.com`

## WebGL Callback Bridge

WebGL path uses `SendMessage` to bridge jslib → C#:

1. `LeaderboardClient.Method()` → registers `TaskCompletionSource` in `LeaderboardCallbackRegistry`
2. jslib function → fetch/GameDashboard → `SendMessage(gameObject, "OnLeaderboardCallback", json)`
3. `AuthManager.OnLeaderboardCallback()` → extracts `bridgeId` → resolves the TCS
4. `LeaderboardClient` awaits TCS → returns `LeaderboardResult`

## Compliance Gates

Before marking Leaderboard implementation complete:
- [ ] Editor: Login → SubmitScore → success
- [ ] Editor: GetLeaderboard → returns ranking array with submitted score
- [ ] Editor: GetGuestLeaderboard → works without login
- [ ] WebGL: SubmitScore via GameDashboard → success
- [ ] WebGL: GetLeaderboard → returns ranking
- [ ] WebGL: GetGuestLeaderboard → works
- [ ] CORS handled via proxy (local) or same-origin (production)
- [ ] Expired token returns clear error
- [ ] No dependency on MultiplayerClient
