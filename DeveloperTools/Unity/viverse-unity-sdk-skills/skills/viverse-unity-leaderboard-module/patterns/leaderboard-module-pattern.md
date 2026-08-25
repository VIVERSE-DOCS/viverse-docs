# Pattern: Live Scoreboard

Use `LeaderboardModule` to maintain a live scoreboard visible to all players in the room during a multiplayer session.

## Problem

In a multiplayer room, every player needs to see everyone else's score updated in real time as gameplay progresses. Polling is too slow; the data must be pushed over the same data channel already in use.

## Solution

Call `mp.Leaderboard.LeaderboardUpdate(score)` whenever the local player's score changes. Subscribe to `mp.Leaderboard.OnLeaderboardUpdate` to receive updates from peers and maintain a local `Dictionary<string, int>` as the source of truth for all scores.

## When To Use

- Post-kill score announcements in shooters
- Combo or streak score updates in rhythm or action games
- Per-round score sync in turn-based competitive games
- Any scenario where all players need a shared, live view of the current ranking

## Code Template

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using ViverseSDK;

/// <summary>
/// Live scoreboard using LeaderboardModule.
/// Attach to a manager GameObject after MultiplayerClient.Init() completes.
/// </summary>
public class LiveScoreboard : MonoBehaviour
{
    private MultiplayerClient _mp;

    // Source of truth: maps PeerId -> current score.
    private Dictionary<string, int> _scores = new Dictionary<string, int>();

    // Optional: UI callback so UI layer can re-draw.
    public event Action<Dictionary<string, int>> OnScoreboardChanged;

    public void Setup(MultiplayerClient mp)
    {
        _mp = mp;

        // Own score starts at zero.
        _scores[_mp.PeerId] = 0;

        // Subscribe before any score broadcast.
        _mp.Leaderboard.OnLeaderboardUpdate += OnRemoteScore;
    }

    // ------- Outbound ---------------------------------------------------

    /// <summary>
    /// Report a new score to all peers. Call on score-change events only,
    /// not from Update().
    /// </summary>
    public void ReportScore(int newScore)
    {
        _scores[_mp.PeerId] = newScore;
        _mp.Leaderboard.LeaderboardUpdate(newScore);
        OnScoreboardChanged?.Invoke(_scores);
    }

    // ------- Inbound ---------------------------------------------------

    private void OnRemoteScore(string json)
    {
        var data = MiniJson.Deserialize(json) as Dictionary<string, object>;
        if (data == null) return;

        string userId = data.ContainsKey("user_id") ? data["user_id"].ToString() : "";
        int score = data.ContainsKey("score") ? Convert.ToInt32(data["score"]) : 0;

        if (string.IsNullOrEmpty(userId)) return;

        _scores[userId] = score;
        OnScoreboardChanged?.Invoke(_scores);

        Debug.Log($"[Scoreboard] {userId} -> {score}");
    }

    // ------- Helpers ---------------------------------------------------

    /// <summary>
    /// Returns a list of (PeerId, score) pairs sorted by score descending.
    /// </summary>
    public List<KeyValuePair<string, int>> GetRanking()
    {
        var sorted = new List<KeyValuePair<string, int>>(_scores);
        sorted.Sort((a, b) => b.Value.CompareTo(a.Value));
        return sorted;
    }

    public void ResetScores()
    {
        _scores.Clear();
        _scores[_mp.PeerId] = 0;
        OnScoreboardChanged?.Invoke(_scores);
    }

    private void OnDestroy()
    {
        if (_mp != null)
            _mp.Leaderboard.OnLeaderboardUpdate -= OnRemoteScore;
    }
}
```

## Integration Points

| Hook | When |
|------|------|
| `Setup(mp)` | After `mp.Init()` returns — before any game logic starts |
| `ReportScore(newScore)` | On score-change event (kill, combo, item collect) |
| `OnScoreboardChanged` | Subscribe in your UI component to redraw the leaderboard table |
| `ResetScores()` | On match restart or new game round |

## Usage Example

```csharp
// After mp.Init() completes:
var board = gameObject.AddComponent<LiveScoreboard>();
board.Setup(mp);
board.OnScoreboardChanged += (scores) => UpdateLeaderboardUI(scores);

// On player kills an enemy:
board.ReportScore(currentScore + 100);

// On match end — read final ranking:
var ranking = board.GetRanking();
for (int i = 0; i < ranking.Count; i++)
    Debug.Log($"Rank {i + 1}: {ranking[i].Key} — {ranking[i].Value} pts");

// Optionally persist final score to global leaderboard:
// await leaderboardClient.SubmitScore("my_meta_name", finalScore.ToString(), token);
```

## Notes

- **Throttle**: Call `ReportScore` on score-change events only — never from `MonoBehaviour.Update()`.
- **Self-echo**: `OnLeaderboardUpdate` fires for remote peers only. Update your own `_scores` entry inside `ReportScore` before broadcasting.
- **Ephemeral**: Scores are lost when the room closes. Call `LeaderboardClient.SubmitScore()` at match end if you need persistent global rankings.
- **No enable flag on init options for LeaderboardModule**: set `modules.leaderboard = new ModuleOption { enabled = true }` to activate it.
