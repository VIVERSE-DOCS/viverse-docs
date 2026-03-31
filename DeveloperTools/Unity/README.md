# Viverse Unity SDK — Documentation Hub

Unity examples and packages for **Viverse**: account login, avatars, cloud saves, leaderboards, and **Play** matchmaking / realtime multiplayer.

**Audience:** Game developers using Unity who are new to Viverse or Play multiplayer and want guided setup and API orientation—not a single-game deep dive.

---

## Suggested reading order

1. [Overview](docs/overview.md) — What ships in which package, folder layout after import  
2. [Configuration](docs/configuration.md) — App ID, `.env`, WebGL, dependencies  
3. [Login](docs/login.md) — Authentication and tokens  
4. [Multiplayer](docs/multiplayer.md) — Matchmaking, game room, modules ([lobby](docs/multiplayer.md#lobby-matchmaking); [game flow phases 2–9](docs/multiplayer.md#game-flow-after-the-lobby))  
5. [Cloud saves](docs/cloud-saves.md) — Persisting player data  
6. [Avatars](docs/avatars.md) — Loading user VRM avatars  
7. [Leaderboards](docs/leaderboards.md) — Score APIs (sample)  
8. [Troubleshooting](docs/troubleshooting.md) — Common issues  

---

## Distribution packages

This directory contains the **`.unitypackage` distributions** for the Viverse Unity SDK and the Play multiplayer example. Import them into a Unity project as needed.

| Package | Filename | Purpose |
|--------|----------|---------|
| **Play / multiplayer example** | `1215_playSDK_example.unitypackage` | Matchmaking, `MultiplayerClient`, WebGL `play-sdk.jslib`, demo scenes |
| **Viverse Unity SDK** | `SDK_v0.96.unitypackage` (or newer) | Login, avatars, cloud save, leaderboard samples |

**Versioning:** Filenames and versions in this table should match the packages in this directory; update the table when you publish new builds.

Import **both** into a Unity project if you need login, cloud saves, or avatars **and** Play multiplayer. Import **only** the Play example if you only need matchmaking / realtime room features.

---

## After import (reference paths)

Paths below assume you imported into a project whose Assets root is `Assets/`.

| Area | Path |
|------|------|
| Play SDK scripts | `Assets/Scripts/PlaySDK/` |
| Viverse samples (login, avatar, cloud, leaderboard) | `Assets/Script/` |
| WebGL bridge (Play) | `Assets/Plugins/WebGL/play-sdk.jslib` |
| Sample scenes | `Assets/Scenes/` |

---

## Sample scenes (included in full SDK import)

| Scene | Topic |
|-------|--------|
| `Login_Sample.unity` | Login |
| `Load_Avatar_Sample.unity` | Avatars |
| `CloudSave_Sample.unity` | Cloud saves |
| `Login_Leaderboard_Sample.unity` | Leaderboards |
| `MatchmakingDemoScene.unity` | Lobby / matchmaking |
| `MultiplayerDemoScene.unity` | Realtime multiplayer |

Open a scene, enter Play Mode (and for WebGL, build and run in the browser where noted).

---

## Module quick reference (Play)

| Component | Role |
|-----------|------|
| `MatchmakingClient` | Lobby: regions, rooms, actors, match flow |
| `MultiplayerClient` | Game session: connect, `SendMessage`, feature modules |
| `GameModule` | Ready, countdown, timers, restart |
| `NetworkSyncModule` | Built-in position / entity sync helpers |
| `ActionSyncModule` | Named competitions / discrete events |
| `LeaderboardModule` | Score updates through the Play client |
| `GeneralModule` | Custom JSON messages (`OnMessage` / routing) |

Details: [Multiplayer](docs/multiplayer.md) — [lobby](docs/multiplayer.md#lobby-matchmaking), [game flow after the lobby](docs/multiplayer.md#game-flow-after-the-lobby).

---

## Support and official resources

- **App ID:** Create and manage worlds/apps in [VIVERSE Studio](https://studio.viverse.com/upload) (see Login doc for where to paste it).  
- Update this README when package filenames or Unity LTS versions change.

**Built with Viverse Play SDK and Viverse Unity SDK**
