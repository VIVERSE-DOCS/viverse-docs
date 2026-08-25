---
name: viverse-unity-auth
description: VIVERSE Unity SDK Authentication — OAuth2 login via dual-path (WebGL viverse-sdk UMD + Editor localhost HttpServer).
prerequisites: [Unity 2021.2+, VIVERSE App ID, WebGL template with viverse-sdk CDN]
tags: [unity, auth, oauth, viverse-sdk, webgl, editor, login]
---

# VIVERSE Unity SDK — Authentication

Integrate VIVERSE OAuth2 authentication into Unity projects targeting WebGL and Editor.

## When To Use This Skill

Use when a Unity project needs:
- User login via VIVERSE account (HTC account)
- Access tokens for API calls (leaderboard, cloud save, matchmaking)
- Dual-path support: WebGL production + Editor development

## Architecture Overview

The SDK uses a **dual-path architecture**:
- **WebGL** (`#if UNITY_WEBGL && !UNITY_EDITOR`): C# calls jslib → jslib uses viverse-sdk UMD from CDN
  - In iframe (viverse.com): `loginWithWorlds()` with App ID
  - Standalone: `loginWithRedirect()` with shared OAuth client UUID
- **Editor** (`#if !UNITY_WEBGL || UNITY_EDITOR`): AuthHttpServer on localhost:40078 serves HTML page that loads viverse-sdk, performs OAuth redirect, POSTs token back

C# is a **thin interface only** — zero business logic.

## Read Order

1. This file (workflow + compliance gates)
2. [patterns/dual-path-auth.md](patterns/dual-path-auth.md)

## Prerequisites

1. Unity 2021.2+ with .NET 4.x runtime
2. App ID from [VIVERSE Studio](https://studio.viverse.com/)
3. For WebGL builds: custom WebGL template with `<script src="https://www.viverse.com/static-assets/viverse-sdk/index.umd.cjs"></script>`

## SDK File Structure

```
Assets/viverse-unity-sdk/
├── Runtime/AuthManager.cs          # Thin C# interface (singleton, events, PlayerPrefs)
├── Runtime/AuthHttpServer.cs       # Editor-only: localhost OAuth server + MainThreadDispatcher
├── Plugins/JSLib/ViverseAuth.jslib # WebGL bridge (loads viverse-sdk, init, login, logout)
└── Sample/AuthTestRunner.cs        # Test UI (auto-initializes, shows login/logout based on state)
```

## Critical Implementation Rules

### OAuth Client ID Selection

| Context | Client ID | Method |
|---------|-----------|--------|
| WebGL in viverse.com iframe | App ID (`5tn6tdp85c`) | `loginWithWorlds()` |
| WebGL standalone (local test) | UUID `42ab6113-acc9-419e-93ca-e0734baf9d3d` | `loginWithRedirect()` |
| Editor (localhost:40078) | UUID `42ab6113-acc9-419e-93ca-e0734baf9d3d` | `loginWithRedirect()` |

The UUID is a shared VIVERSE development OAuth client registered to allow localhost redirects. App IDs are NOT registered OAuth clients — they only work with `loginWithWorlds()` inside the viverse.com iframe.

### Constructor Must Be Capital C

```javascript
new window.viverse.Client({ clientId, domain })  // CORRECT
new window.viverse.client({ clientId, domain })  // WRONG (deprecated)
```

### Token Storage

Tokens stored in PlayerPrefs:
- `viverse_access_token` — JWT access token
- `viverse_account_id` — UUID account identifier  
- `viverse_expires_in` — Token lifetime in seconds

### Token Usage for API Calls

All VIVERSE APIs use custom headers (NOT `Authorization: Bearer`):
- JWT token → header: `AccessToken: <jwt>`
- Non-JWT (AuthKey) → header: `AuthKey: <key>`

Detection: `token.Split('.').Length == 3` → JWT, else AuthKey.

### Auth Flow (WebGL)

1. `AuthManager.Initialize(appId)` → loads viverse-sdk UMD from CDN
2. `ViverseAuth_InitClient` → detects iframe vs standalone, selects clientId
3. `ViverseAuth_CheckAuth` → checks cached auth OR handles redirect callback
4. If not logged in → user clicks Login → `loginWithWorlds()` or `loginWithRedirect()`
5. On success → `OnAuthSuccess` callback → saves to PlayerPrefs → fires `OnLoginSuccess` event

### Auth Flow (Editor)

1. `AuthManager.Initialize(appId)` → creates AuthHttpServer, starts MainThreadDispatcher
2. User clicks Login → `AuthHttpServer.StartServerAndLogin()` → opens browser to localhost:40078
3. Browser loads viverse-sdk, creates Client with UUID, calls `loginWithRedirect()`
4. User authenticates → redirected back to localhost:40078 with `?code=&state=`
5. Page calls `handleRedirectCallback()` → POSTs token to `/api/viverse/auth/callback`
6. AuthHttpServer receives token → dispatches to main thread → fires `OnLoginSuccess`

## Public API

```csharp
namespace ViverseSDK
{
    public class AuthManager : MonoBehaviour
    {
        public static AuthManager Instance { get; }
        
        // State
        public bool IsLoggedIn { get; }
        public string AccessToken { get; }
        public string AccountId { get; }
        
        // Events
        public event Action<string> OnStatusChanged;
        public event Action<AuthResult> OnLoginSuccess;
        public event Action OnLogout;
        public event Action<string> OnError;
        
        // Methods
        public void Initialize(string appId);
        public void Login();
        public void Logout();
    }
    
    [Serializable]
    public class AuthResult
    {
        public string access_token;
        public string account_id;
        public int expires_in;
    }
}
```

## Integration Example

```csharp
using ViverseSDK;

public class MyGame : MonoBehaviour
{
    void Start()
    {
        var auth = AuthManager.Instance;
        auth.OnLoginSuccess += (result) => {
            Debug.Log($"Logged in: {result.account_id}");
            // Now use result.access_token for API calls
        };
        auth.Initialize("YOUR_APP_ID");
    }
    
    public void OnLoginButtonClicked()
    {
        if (!AuthManager.Instance.IsLoggedIn)
            AuthManager.Instance.Login();
    }
}
```

## Compliance Gates

Before marking auth implementation complete:
- [ ] Editor: Initialize → Login → token appears in UI
- [ ] Editor: Logout clears token and PlayerPrefs
- [ ] WebGL (standalone): Initialize → checkAuth finds no session → Login redirects → returns with token
- [ ] WebGL (iframe): loginWithWorlds triggers parent frame auth flow
- [ ] Token persists across scene loads (DontDestroyOnLoad + PlayerPrefs)
- [ ] AuthTestRunner shows login button only when not logged in
