---
description: >-
  Legacy VIVERSE Unity SDK v0.96 example. LeaderboardService is retired.
  Use LeaderboardClient in the VIVERSE Unity SDK for C#.
---

# Unity Leaderboard example — legacy

This example documented VIVERSE Unity SDK v0.96. `LeaderboardService` is a legacy API and should not be used for new Unity projects.

## Current Unity implementation

**VIVERSE Unity SDK 1.2 · C# · `ViverseSDK`**

Use `LeaderboardClient`. Create the leaderboard in VIVERSE Studio first. The `metaName` argument must match the name configured there.

```csharp
// VIVERSE Unity SDK 1.2 — Unity C#
using ViverseSDK;

var leaderboard = new LeaderboardClient("YOUR_APP_ID");
string token = AuthManager.Instance.AccessToken;

await leaderboard.SubmitScore("time_attack", "100", token);
var mine = await leaderboard.GetLeaderboard("time_attack", token);
if (mine.success) Debug.Log($"My ranking: {mine.data}");
```

Leaderboard setup is on the [VIVERSE Unity SDK](../viverse-unity-sdk.md) page.
