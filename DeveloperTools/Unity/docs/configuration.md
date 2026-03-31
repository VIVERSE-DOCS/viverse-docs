# Configuration

## Who this is for

Anyone importing the Play or Viverse Unity SDK packages who needs App ID, service URLs, WebGL setup, and optional dependencies.

---

## Unity version

Use an **LTS Unity version** compatible with your project and the package (check release notes shipped with each `.unitypackage`). Open the project, resolve any package import warnings, then open the sample scenes listed in the hub [README](../README.md).

---

## App ID (world / application)

- Create content in **VIVERSE Studio** and obtain an **App ID** for your world/experience.  
- **Login sample:** set the App ID on `LoginSample` (inspector) or the component that calls `LoginManager.Initialize` — see [Login](login.md).  
- **Play demos:** set App ID in the matchmaking / multiplayer demo UI (`MatchmakingDemoManager`, `MultiplayerDemoManager`) or your own bootstrap code.

Without a valid App ID, login and Play initialization will fail or return errors.

---

## Play SDK environment (`.env`)

`PlaySDK.Config.EnvConfig` loads optional environment overrides from:

`Assets/Scripts/PlaySDK/.env`

If the file is missing, **built-in production defaults** apply for:

- `WEBRTCBOT_SERVICE_API_URL`  
- `MATCHMAKING_SERVICE_WS_URL`  
- `MULTIPLAYER_PROXY_WS_URL`  

To use **stage** or **dev** gateways, copy the commented blocks from `EnvConfig.cs` into `.env` or edit the dictionary in code per your pipeline policy.

Never commit secrets; `.env` should stay local or come from your CI secrets manager.

---

## WebGL

- **Play:** `play-sdk.jslib` loads the Viverse / Play JavaScript SDK from the CDN at runtime in the browser. Test with a **WebGL build**, not only the Editor.  
- **Login:** `LoginManager` uses `DllImport("__Internal")` into **VIVERSE_**\* functions provided by the WebGL template / plugins bundled with the Viverse SDK package. Use the template or plugins that ship with the sample.  
- Keep **one** active UI input module in WebGL builds to avoid duplicate event systems.

---

## Dependencies

| Dependency | Used by |
|------------|---------|
| **TextMeshPro** | Demo UI |
| **Newtonsoft.Json** | Play samples and JSON payloads |
| **UniVRM** (0.x or 1.0 + define symbols) | [Avatars](avatars.md) — `AvatarManager` |

For UniVRM, follow `AvatarManager` warnings: add scripting define **`VRM_0_X`** or **`VRM_1_0`** (or `UNIVRM_*` as applicable) after installing UniVRM.

---

## Localhost login helper

`HttpServer` is used for redirect-based auth during **local / Editor** testing. Ensure a scene that uses login includes or creates `HttpServer` as described in [Login](login.md).

---

## Next steps

- [Login](login.md) — tokens in `PlayerPrefs`  
- [Multiplayer](multiplayer.md) — proxy and matchmaking URLs  
