# Login

## Who this is for

Developers using the **Viverse Unity SDK** package who need a **VIVERSE / HTC account** access token and account id for authenticated APIs (avatars, cloud save, leaderboard sample, etc.).

**Package:** Viverse Unity SDK (e.g. `SDK_v0.96.unitypackage`).  
**Sample scene:** `Assets/Scenes/Login_Sample.unity`

---

## What the sample does

- **`LoginManager`** (`namespace ViverseSDK.Login`) — Core auth: loads JS SDK on WebGL, initiates login, stores **`access_token`**, **`account_id`**, **`expires_in`** in `PlayerPrefs`.  
- **`LoginSample`** — UI wiring, optional **auto login**, status text, token preview.  
- **`HttpServer`** — Helps complete OAuth-style redirect on **localhost** / Editor flows by listening for the callback.

Login is **not** a separate “username/password inside Unity” form for all paths; on WebGL the **browser / Viverse client** drives the actual sign-in UI via the loaded SDK.

---

## Quick how-to

1. Import the Viverse Unity SDK package.  
2. Open **`Login_Sample.unity`**.  
3. Assign your **App ID** on `LoginSample` (inspector field documented in code — from VIVERSE Studio).  
4. Ensure **`LoginManager`** exists on the same GameObject or is added by `LoginSample` at runtime.  
5. For WebGL: build with the **WebGL template / plugins** included with the SDK so `VIVERSE_*` native imports resolve.  
6. Press **Login** (or enable **auto login** on `LoginSample` for testing).  
7. On success, read tokens from `LoginManager`:

```csharp
var token = loginManager.AccessToken;
var accountId = loginManager.AccountId;
bool loggedIn = loginManager.IsLoggedIn;
```

---

## Stored keys (`PlayerPrefs`)

| Key | Meaning |
|-----|---------|
| `access_token` | Bearer-style token for API calls |
| `account_id` | Account identifier |
| `expires_in` | Expiry hint from auth result |

Use **`LoginManager.ClearLoginData()`** (or the sample’s clear button) to reset.

---

## Using the token elsewhere

- **Avatars:** pass `accessToken` into `AvatarManager.LoadDefaultAvatar` — [Avatars](avatars.md).  
- **Cloud saves:** `CloudSaveService` / sample expects a logged-in user — [Cloud saves](cloud-saves.md).  
- **Leaderboard sample:** reads `access_token` from `PlayerPrefs` — [Leaderboards](leaderboards.md).

---

## Platform notes

- **WebGL:** Watch the **browser developer console** for SDK load and auth messages; `LoginSample` logs hints.  
- **Editor / standalone:** `HttpServer` may be required for the redirect URI flow; the sample can create one if missing.

---

## See also

- [Configuration](configuration.md)  
- [Troubleshooting](troubleshooting.md) — empty App ID, WebGL plugin errors  
