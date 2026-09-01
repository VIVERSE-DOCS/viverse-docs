---
description: >-
  Explains the three GitHub artifacts — the `.unitypackage`, the README, and the
  AI skills pack — who each one is for, and where to go next.
---

# VIVERSE Unity SDK

## Introduction

The VIVERSE Unity SDK lets you ship VIVERSE-connected Unity games to WebGL and iterate them in the Editor with the same C# API. Version 1.2 covers authentication, Lambda functions, matchmaking, real-time multiplayer, cloud save, leaderboards, achievements, and avatars.

This page is the entry point for the artifacts published on GitHub. It answers three questions:

* What are these three artifacts?
* Who should use each one?
* Where do I go next?

The [README](https://github.com/VIVERSE-DOCS/viverse-docs/blob/main/DeveloperTools/Unity/README.md) remains the human-facing implementation and API guide. This overview does not duplicate those C# samples.

## Prerequisites

* Unity 2021.2 or newer with the .NET 4.x scripting runtime
* WebGL build support installed through Unity Hub
* A VIVERSE App ID from [VIVERSE Studio](https://studio.viverse.com/)
* Optional: [UniVRM v0.130.1 or newer](https://github.com/vrm-c/UniVRM) if you want to render downloaded VRM models. The SDK compiles and runs without it.
* Optional: [Cursor](https://cursor.com/) if you want to use the AI skills pack

## The three artifacts at a glance

These three artifacts live together under `DeveloperTools/Unity` on GitHub. Two are files (the `.unitypackage` and the README). The third is the `viverse-unity-sdk-skills` folder. They are not three versions of the same documentation.

| Artifact                 | What it is                                      | You use it when                                  |
| ------------------------ | ----------------------------------------------- | ------------------------------------------------ |
| SDK v1.2 `.unitypackage` | The plugin you import into Unity                | You need the SDK running in a project            |
| README                   | The human-facing getting-started and API guide  | You need to learn or look up C# APIs             |
| AI skills folder         | Instructions for Cursor and other coding agents | You want an agent to implement the SDK correctly |

| Artifact         | GitHub                                                                                                                                                   |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SDK v1.2 package | [Viverse-Unity-SDK-1.2.0.unitypackage](https://github.com/VIVERSE-DOCS/viverse-docs/blob/main/DeveloperTools/Unity/Viverse-Unity-SDK-1.2.0.unitypackage) |
| README           | [README.md](https://github.com/VIVERSE-DOCS/viverse-docs/blob/main/DeveloperTools/Unity/README.md)                                                       |
| AI skills folder | [viverse-unity-sdk-skills](Unity/viverse-unity-sdk-skills)                                                                                               |

{% hint style="info" %}
**Start with the package.** The README and AI skills describe how to use the SDK. Neither one adds the SDK to Unity by itself.
{% endhint %}

## SDK v1.2 package

The `.unitypackage` is the actual Unity plugin. Download it, import it, and it places C# clients, WebGL bridges, and assemblies under `Assets/viverse-unity-sdk/`. Without this file, nothing runs.

Download: [Viverse-Unity-SDK-1.2.0.unitypackage](https://github.com/VIVERSE-DOCS/viverse-docs/blob/main/DeveloperTools/Unity/Viverse-Unity-SDK-1.2.0.unitypackage)

### What you get

The SDK ships a thin C# surface on top of the viverse-sdk JavaScript library. In WebGL builds, C# calls jslib bridges that delegate to the CDN-hosted viverse-sdk. In the Editor, the same C# API talks directly to VIVERSE REST endpoints and WebSocket gateways, so you can develop and test without a browser.

| Feature        | What it does                                                                                              |
| -------------- | --------------------------------------------------------------------------------------------------------- |
| Authentication | Signs a player in with their VIVERSE account through OAuth 2.0.                                           |
| Lambda         | Invokes server-side functions you register in VIVERSE Studio.                                             |
| Matchmaking    | Creates, discovers, and joins rooms over WebSocket.                                                       |
| Multiplayer    | Runs WebRTC data channels for real-time sync, action, leaderboard, and general modules.                   |
| Cloud Save     | Persists versioned save files and key-value player data.                                                  |
| Leaderboard    | Submits scores and reads rankings, with RSA/AES payload encryption.                                       |
| Achievements   | Fetches and unlocks achievements, with the same encryption pipeline.                                      |
| Avatar         | Reads a player's profile and avatar list, downloads VRM models, and (optionally) renders them via UniVRM. |

After import, the SDK lands under `Assets/viverse-unity-sdk/` and adds three assemblies:

* `Viverse.SDK` — runtime
* `Viverse.SDK.Editor` — editor tooling
* `Viverse.NativeWebSocket` — a vendored WebSocket transport, renamed so it can coexist with the community `NativeWebSocket` package

{% hint style="info" %}
If your project already uses the community NativeWebSocket package, see **Coexisting with an existing NativeWebSocket** in the [README](https://github.com/VIVERSE-DOCS/viverse-docs/blob/main/DeveloperTools/Unity/README.md).
{% endhint %}

### Import the package

{% stepper %}
{% step %}
### Download the package

Download [Viverse-Unity-SDK-1.2.0.unitypackage](https://github.com/VIVERSE-DOCS/viverse-docs/blob/main/DeveloperTools/Unity/Viverse-Unity-SDK-1.2.0.unitypackage) from GitHub.
{% endstep %}

{% step %}
### Import into Unity

In Unity, open **Assets > Import Package > Custom Package** and select the file.
{% endstep %}

{% step %}
### Confirm the import

Leave every item checked in the import dialog and select **Import**. Wait for Unity to finish recompiling. You should see zero errors in the Console.
{% endstep %}
{% endstepper %}

For C# samples, WebGL build notes, local testing, and the full API reference, use the README.

## README

The README is the human-facing implementation and API guide. A person reads it in GitHub or in GitBook after you upload it. It is not imported into Unity.

Open it: [Getting started with the VIVERSE Unity SDK](https://github.com/VIVERSE-DOCS/viverse-docs/blob/main/DeveloperTools/Unity/README.md)

Use the README when you need to:

* Install and authenticate a player
* Call a specific feature with C# examples
* Look up types, events, and methods
* Build and test WebGL locally
* Understand the project layout under `Assets/viverse-unity-sdk/`

The README currently covers:

| Section                               | What it contains                              |
| ------------------------------------- | --------------------------------------------- |
| What you get / Requirements / Install | Baselines and how to import the package       |
| Authenticate a player                 | `AuthManager` OAuth flow for WebGL and Editor |
| Invoke Lambda functions               | `LambdaClient` and Studio `reply(...)` notes  |
| Match players into rooms              | `MatchmakingClient` WebSocket lifecycle       |
| Sync players in real time             | `MultiplayerClient` and modules               |
| Save player progress to the cloud     | `CloudSaveClient`                             |
| Submit scores and read rankings       | Persistent REST leaderboard                   |
| Fetch and unlock achievements         | `AchievementsClient`                          |
| Load a player's avatar                | `AvatarClient` and optional UniVRM            |
| Build / test WebGL                    | Template, local server, CORS proxy            |
| API reference                         | Member tables for the public C# surface       |
| Project layout                        | Folder map of `Assets/viverse-unity-sdk/`     |

{% hint style="info" %}
**Treat the README as the source of truth for APIs.** This overview stays short on purpose so it does not go stale when samples in the repo change.
{% endhint %}

## AI skills

The AI skills pack is a set of instructions for Cursor and other coding agents. Each skill folder teaches the agent when to use a feature, which C# types to call, how the WebGL vs Editor paths work, and which rules not to break.

AI skills help the coding agent use the SDK correctly; they do not add runtime functionality to the Unity project.

They are not:

* A second Unity package
* A substitute for the README
* Something you import through **Assets > Import Package**

Open the pack: [viverse-unity-sdk-skills](Unity/viverse-unity-sdk-skills)

### When to use them

Use the skills when you are implementing VIVERSE Unity features with Cursor (or a similar agent) and you want the agent to follow the official dual-path architecture instead of inventing a different integration.

If you are writing the integration yourself, the README is enough. The skills are optional.

### How to add them in Cursor

Each folder under `viverse-unity-sdk-skills/skills/` is one skill (`SKILL.md`, `skill.json`, `rules.json`, and optional `patterns/`). Copy those folders into Cursor's skills location:

| Type     | Path                                                           | Scope                              |
| -------- | -------------------------------------------------------------- | ---------------------------------- |
| Project  | `.cursor/skills/` in your Unity project                        | Shared with anyone using that repo |
| Personal | `~/.cursor/skills/` (Windows: `%USERPROFILE%\.cursor\skills\`) | Available across your projects     |

{% stepper %}
{% step %}
### Get the skill folders

Clone or download [viverse-unity-sdk-skills](Unity/viverse-unity-sdk-skills) from GitHub.
{% endstep %}

{% step %}
### Copy each skill into Cursor

Copy every folder inside `skills/` (for example `viverse-unity-auth`) into `.cursor/skills/` or your personal skills directory. Keep the folder names. Do not copy the parent `viverse-unity-sdk-skills` wrapper as a single skill.
{% endstep %}

{% step %}
### Use them while coding

Ask Cursor to implement the VIVERSE feature you need. The agent should pick up the matching skill (auth, matchmaking, multiplayer, and so on) and follow its API, dual-path rules, and compliance checklist.
{% endstep %}
{% endstepper %}

### Skill catalog

Descriptions below are taken from each skill's `skill.json`. Multiplayer module skills require an active room from matchmaking first.

#### Core and REST

| Skill                       | Depends on           | What it covers                                                                                                                                                                                      |
| --------------------------- | -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `viverse-unity-auth`        | —                    | VIVERSE Unity SDK Authentication — OAuth2 login via dual-path architecture (WebGL viverse-sdk UMD + Editor localhost HttpServer redirect).                                                          |
| `viverse-unity-lambda`      | `viverse-unity-auth` | VIVERSE Unity SDK Lambda — Invoke server-side functions via REST, dual-path (WebGL fetch + Editor UnityWebRequest).                                                                                 |
| `viverse-unity-cloudsave`   | `viverse-unity-auth` | VIVERSE Unity SDK Cloud Save — Persistent player data storage via REST, dual-path (WebGL fetch + Editor UnityWebRequest). Two patterns: versioned saves + key-value.                                |
| `viverse-unity-leaderboard` | `viverse-unity-auth` | VIVERSE Unity SDK Leaderboard — Score submission (RSA/AES encrypted) and ranking retrieval via REST, dual-path (WebGL viverse-sdk GameDashboard + Editor UnityWebRequest with Ironhide encryption). |
| `viverse-unity-avatar`      | `viverse-unity-auth` | VIVERSE Unity SDK Avatar — User profiles, avatar lists, VRM model download & loading. Dual-path (WebGL Avatar SDK + Editor UnityWebRequest). Includes VRM 3D rendering with UniVRM10.               |
| `viverse-unity-matchmaking` | —                    | Unity Matchmaking — room create/join/leave, actor management, and real-time room events via dual-path architecture (WebGL viverse-sdk UMD + Editor NativeWebSocket). Session ID auto-generated.     |

#### Real-time multiplayer

| Skill                              | Depends on                  | What it covers                                                                                                                                                                                                            |
| ---------------------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `viverse-unity-multiplayer`        | `viverse-unity-matchmaking` | Unity Play SDK Multiplayer — real-time data channels, 6 game modules (Game, NetworkSync, ActionSync, Leaderboard, Lambda, General), and master/client architecture via dual-path (WebGL Mediasoup + Editor WebRTC proxy). |
| `viverse-unity-game-module`        | matchmaking + multiplayer   | Unity Play SDK Game Module — manages multiplayer game lifecycle (ready, countdown, start, end, restart) through the GameModule property on MultiplayerClient.                                                             |
| `viverse-unity-network-sync`       | matchmaking + multiplayer   | Unity Play SDK NetworkSync Module — continuous per-frame position and transform synchronization for players and game entities via the NetworkSyncModule property on MultiplayerClient.                                    |
| `viverse-unity-action-sync`        | matchmaking + multiplayer   | Unity Play SDK ActionSync Module — broadcasts discrete competitive game actions (attacks, abilities, emotes) to all peers with caller-supplied action IDs for de-duplication.                                             |
| `viverse-unity-leaderboard-module` | matchmaking + multiplayer   | Unity Play SDK Leaderboard Module — broadcasts real-time in-room scores to all peers via WebRTC data channel during an active multiplayer session (ephemeral, not persisted to backend).                                  |
| `viverse-unity-general-module`     | matchmaking + multiplayer   | Unity Play SDK General Module — sends and receives arbitrary freeform messages between peers over the WebRTC data channel. Also fires connection/disconnection events for all room participants.                          |

{% hint style="warning" %}
**Two different leaderboards**

* `viverse-unity-leaderboard` is the **persistent REST** ranking you configure in VIVERSE Studio. Scores survive after the session ends.
* `viverse-unity-leaderboard-module` is **in-room ephemeral** scores sent over the multiplayer data channel. They are not stored on the backend.
{% endhint %}

Use `viverse-unity-multiplayer` first to connect and call `Init()`. Then use a module-specific skill when you need the detailed pattern for that module.

## Suggested order

{% stepper %}
{% step %}
### Import the SDK

Download and import [Viverse-Unity-SDK-1.2.0.unitypackage](https://github.com/VIVERSE-DOCS/viverse-docs/blob/main/DeveloperTools/Unity/Viverse-Unity-SDK-1.2.0.unitypackage) so the C# API exists in your project.
{% endstep %}

{% step %}
### Read the README for the feature you need

Open the [README](https://github.com/VIVERSE-DOCS/viverse-docs/blob/main/DeveloperTools/Unity/README.md) for install details, C# samples, and the API reference. Most features need an access token from `AuthManager` first. The public avatar catalog is the documented exception.
{% endstep %}

{% step %}
### Optionally add AI skills

If you use Cursor, copy the skill folders from [viverse-unity-sdk-skills](Unity/viverse-unity-sdk-skills) into `.cursor/skills/`. This helps the agent follow the official APIs. It does not replace importing the package.
{% endstep %}
{% endstepper %}

## Where to go next

* Get an App ID and publish from [VIVERSE Studio](https://studio.viverse.com/).
* Explore the sample scenes under `Assets/viverse-unity-sdk/Sample/` after you import the package. `ViverseTestRunner` wires every feature for interactive testing.
* Use the [README](https://github.com/VIVERSE-DOCS/viverse-docs/blob/main/DeveloperTools/Unity/README.md) as the day-to-day API reference.
