# Leaderboards

## Who this is for

Developers who want a **sample** integration for leaderboard-style APIs alongside **login**.

**Package:** Viverse Unity SDK (e.g. `SDK_v0.96.unitypackage`).  
**Sample scene:** `Assets/Scenes/Login_Leaderboard_Sample.unity`

---

## Play SDK note

The **Play** `MultiplayerClient` also exposes **`LeaderboardModule`** for **in-session** score updates (`LeaderboardUpdate`). That is separate from the **HTTP leaderboard sample** in `LeaderboardService` — use Play when scores are tied to the active game room; use the Viverse sample when following the platform REST pattern bundled with the SDK.

---

## Quick how-to

1. Open **`Login_Leaderboard_Sample.unity`**.  
2. Log in so **`PlayerPrefs`** contains **`access_token`** — [Login](login.md).  
3. Use the sample UI wired to **`LeaderboardSample`** / **`LeaderboardService`** to submit or query scores per the implementation.

Inspect **`LeaderboardSample.cs`** and **`LeaderboardService.cs`** for endpoints, request bodies, and error handling.

---

## See also

- [Login](login.md)  
- [Multiplayer](multiplayer.md) — `LeaderboardModule`  
