# Avatars

## Who this is for

Developers who want to **load the current user’s VRM avatar** from Viverse into a Unity scene.

**Package:** Viverse Unity SDK (e.g. `SDK_v0.96.unitypackage`).  
**Sample scene:** `Assets/Scenes/Load_Avatar_Sample.unity`

---

## Prerequisites

1. **Login** — Obtain an **access token** from `LoginManager` / `LoginSample` — [Login](login.md).  
2. **UniVRM** — Install [UniVRM](https://github.com/vrm-c/UniVRM/releases) (0.x or 1.0).  
3. **Scripting define symbols** — Add **`VRM_0_X`** or **`VRM_1_0`** (or the `UNIVRM_*` variants your install expects) in **Player Settings → Script Compilation** so `AvatarManager` compiles the correct import path.

Without UniVRM, `AvatarManager` logs errors and **will not** instantiate a model.

---

## What the sample does

- **`AvatarManager`** — Calls the Viverse avatar list API, picks the **current** or first usable avatar, optionally **decrypts** binary VRM data, then loads via **UniVRM** into `avatarParent`.  
- **`AvatarSample`** — Example wiring (buttons, status) for the sample scene.

API base used in code: `https://sdk-api.viverse.com/api/meetingareaselector/v1/newgenavatar/getavatarlist` (see `AvatarManager` constants).

---

## Quick how-to

1. Complete **login** and confirm `access_token` is present.  
2. Open **`Load_Avatar_Sample.unity`**.  
3. Assign **`avatarParent`** and wire UI to call `LoadDefaultAvatar(accessToken)`.  
4. Enter Play Mode; you should see status messages then a spawned VRM under the scene hierarchy.

```csharp
// Pseudocode — get token from your LoginManager
string token = loginManager.AccessToken;
avatarManager.LoadDefaultAvatar(token);
```

Subscribe to **`OnAvatarLoaded`**, **`OnError`**, **`OnStatusUpdated`** on `AvatarManager` for production UX.

---

## Encryption

Some avatars are returned with **encryption metadata**. `AvatarManager` uses `SimpleAvatarEncryptUtility` and response headers to decrypt when needed. If loading fails, check the console for parse/decrypt errors and token expiry.

---

## See also

- [Login](login.md)  
- [Configuration](configuration.md) — define symbols  
- [Troubleshooting](troubleshooting.md)  
