# Overview

## What these packages are

Viverse splits common game concerns into:

1. **Platform identity & content** — Sign in, optional VRM avatars, cloud-stored app data, and related HTTP APIs. These ship in the **Viverse Unity SDK** package (e.g. `SDK_v0.96.unitypackage`).
2. **Realtime Play** — Lobby matchmaking and in-session networking (rooms, sync modules, custom messages). These ship in the **Play SDK example** package (e.g. `1215_playSDK_example.unitypackage`).

You can use either package alone, or both, depending on your game.

---

## Package comparison

| Capability | Play example package | Viverse Unity SDK package |
|------------|----------------------|---------------------------|
| Matchmaking / rooms | Yes (`MatchmakingClient`) | No |
| Multiplayer game session | Yes (`MultiplayerClient`) | No |
| Login / access token | No* | Yes (`LoginManager`, `LoginSample`) |
| Avatars (VRM) | No | Yes (`AvatarManager`, `AvatarSample`) |
| Cloud save & UserApp samples | No** | Yes (`CloudSaveService`, `CloudSaveSample`) |
| Leaderboard sample | Play has `LeaderboardModule` in-session | Sample scene + `LeaderboardService` |

\* The Play stack may run in a Viverse-hosted context where the browser already has a session; the **C# login sample** comes with the Viverse SDK package.  
\** The Play folder may include **`WebRTCService`** helpers (e.g. room/game/userapp HTTP) for advanced integration; the **guided cloud save UI** is in the Viverse SDK package.

---

## Folder layout after import

### Play example → under `Assets/`

- `Scripts/PlaySDK/` — `MatchmakingClient`, `MultiplayerClient`, modules, demos, types, `Services/WebRTCService.cs`, config  
- `Plugins/WebGL/play-sdk.jslib` — Loads Viverse / Play JS SDK for WebGL  
- `Scenes/MatchmakingDemoScene.unity`, `MultiplayerDemoScene.unity`  

### Viverse Unity SDK → under `Assets/`

- `Script/` — `LoginManager`, `LoginSample`, `AvatarManager`, `CloudSaveService`, `LeaderboardService`, `HttpServer`, etc.  
- `Scenes/` — `Login_Sample`, `Load_Avatar_Sample`, `CloudSave_Sample`, `Login_Leaderboard_Sample`  

> **Note:** Play scripts use `Assets/Scripts/PlaySDK/` (plural **Scripts**). Viverse samples use `Assets/Script/` (singular). This mirrors how the packages were authored; plan merges or asmdefs if you need strict layout.

---

## Typical game flow (both packages)

1. **Configure** App ID and environment (see [Configuration](configuration.md)).  
2. **Login** (optional but required for avatar list and cloud save APIs tied to the user) — [Login](login.md).  
3. **Matchmaking** — create or join a room; obtain `game_session`, `app_id`, `session_id`.  
4. **Multiplayer** — connect `MultiplayerClient` with those IDs; enable modules and gameplay.  
5. **Cloud save / avatars** — call services with the same access token when needed — [Cloud saves](cloud-saves.md), [Avatars](avatars.md).

---

## Next steps

- [Configuration](configuration.md)  
- [Multiplayer](multiplayer.md) for lobby → room handoff  
- [Login](login.md) if you use Viverse SDK features that require tokens  
