---
description: >-
  Introducing the SDKs and services that are available to developers making 3D
  projects on VIVERSE
---

# Introduction to Developer Tools

***

> _**NOTE:** VIVERSE SDKs cannot be used with projects published via the_ [_PlayCanvas Create SDK extension_](https://docs.viverse.com/playcanvas-sdk/playcanvas-extension-setup)_, which do not have App IDs._

## How Developers Use Our Tools

Our goal is to make it simple to publish rich experiences to VIVERSE. These tools help our creator community build more advanced 3D experiences, including multiplayer games, and utilize the account and avatar system to give users a more seamless experience when hopping from world to world in VIVERSE.

While we do not require developers use these tools — and while we also make it possible for developers to include their own servers/databases/external APIs in VIVERSE projects — we highly recommend that all creators familiarize themselves with these offerings and consider integrating the VIVERSE account & avatar system into their projects. It makes the experience better for our users and many of these services are available for free!

## Choose your platform

Pick Unity or JavaScript first. Feature pages and engine examples stay under the platform you choose.

### Unity

| Name | Description |
| --- | --- |
| [VIVERSE Unity SDK](viverse-unity-sdk.md) | C# SDK for Unity. Import the v1.2 package for authentication, Lambda, matchmaking, multiplayer, cloud save, leaderboards, achievements, and avatars. |

### JavaScript / Web

One JavaScript SDK for JavaScript/Web projects, including PlayCanvas and three.js. PlayCanvas and three.js tutorials are under each feature in the sidebar.

| Name | Description |
| --- | --- |
| [Login & Authentication](login-and-authentication-for-the-sdk/) [Beta] | Get a user's account information when they join your experience on VIVERSE. This will allow you to access their display name, avatar information, and account information, making it easier for end-users to travel between VIVERSE experiences while staying connected to their identity and friends. |
| [Avatar SDK](avatar-sdk.md) [Beta] | Download and use a user's avatar file in your VIVERSE experience. Digital identity is an important consideration in 3D and including end-users' avatars makes them feel more at home in your VIVERSE World. |
| [Leaderboard SDK](leaderboard-sdk/) [Beta] | Access and save information about players interacting with your world. Keep track of high scores to boost engagement with your player base. |
| [Matchmaking & Networking](matchmaking-and-networking-sdk.md) [Beta] | Save and network game-state between clients in your VIVERSE world. Use this SDK to build richer multiplayer experiences. |
| [Storage SDK](storage-sdk.md) | Persist player data with cloud save. |

## SDK versioning

The JavaScript SDK and the Unity SDK are versioned separately.

Latest JavaScript SDK: [v1.3.3](https://www.viverse.com/static-assets/viverse-sdk/1.3.3/index.umd.cjs) (2025-11-20). [Change log](CHANGELOG.md)

The Unity SDK current package is v1.2. See the [VIVERSE Unity SDK](viverse-unity-sdk.md) page.

## Provisioning Your Own Game Servers & Services

While the above services are available for free to our developers to use, we frequently allow developers to include their own, externally hosted services in their VIVERSE creations. If you have an API endpoint, database, or hosted gameserver that you would like to access in VIVERSE, please email michael\_morran@htc.com and james\_kane@htc.com OR join our [Discord Server](https://discord.gg/viversecreators) and message us with more information about the nature of your service and the URL you would like whitelisted!
