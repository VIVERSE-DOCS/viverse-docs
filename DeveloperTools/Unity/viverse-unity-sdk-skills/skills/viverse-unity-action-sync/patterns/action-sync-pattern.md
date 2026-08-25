# ActionSync Pattern — Attack and Ability Broadcast

## Overview

This pattern shows a full combat system using `ActionSyncModule.Competition`.
It covers: generating unique action IDs, sending actions, receiving and de-duplicating
them, routing by action name, and applying effects to game objects.

## Key Design Principles

- **One action ID per action instance.** Generate a fresh GUID fragment before each `Competition()` call.
- **De-dup on receive.** Store processed IDs in a `HashSet`. The broadcast reaches every peer exactly once, but the HashSet guards against any edge-case replay.
- **Action vs state.** ActionSync carries event notifications. Game state (current HP, buffs) lives elsewhere — don't try to compute final state from action messages alone.
- **Thin `action_msg`.** Keep the payload small. A compact JSON string is fine; avoid embedding full game state.

## CombatSystem Implementation

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using ViverseSDK;

/// <summary>
/// Full ActionSync combat system: send attacks/abilities + receive and apply remote actions.
/// Wired after MultiplayerClient.Init completes.
/// </summary>
public class CombatSystem : MonoBehaviour
{
    private MultiplayerClient _mp;

    // De-dup set — clear between matches to prevent unbounded growth.
    private readonly HashSet<string> _seen = new HashSet<string>();

    // Map userId -> local proxy object (e.g., remote player GameObject)
    private Dictionary<string, GameObject> _playerObjects = new Dictionary<string, GameObject>();

    public void Initialize(MultiplayerClient mp)
    {
        _mp = mp;
        _mp.ActionSync.OnCompetition += HandleCompetition;
        Debug.Log("[ActionSync] CombatSystem ready");
    }

    // ─── Send ─────────────────────────────────────────────────────────────────────

    /// <summary>
    /// Broadcast a melee or ranged attack.
    /// </summary>
    public void SendAttack(string targetId, int damage, string weaponType)
    {
        string actionId  = NewActionId();
        string actionMsg = $"{{\"target\":\"{targetId}\",\"dmg\":{damage},\"weapon\":\"{weaponType}\"}}";
        _mp.ActionSync.Competition("attack", actionMsg, actionId);
        Debug.Log($"[ActionSync] Attack sent → target={targetId} dmg={damage} id={actionId}");
    }

    /// <summary>
    /// Broadcast an ability use (AoE, buff, debuff, etc.).
    /// </summary>
    public void SendAbility(string abilityId, string targetId = "")
    {
        string actionId  = NewActionId();
        string actionMsg = string.IsNullOrEmpty(targetId)
            ? $"\"{{\\\"ability\\\":\\\"{abilityId}\\\"}}\""
            : $"{{\"ability\":\"{abilityId}\",\"target\":\"{targetId}\"}}";
        _mp.ActionSync.Competition("ability", actionMsg, actionId);
        Debug.Log($"[ActionSync] Ability sent: {abilityId} id={actionId}");
    }

    /// <summary>
    /// Broadcast an emote (no game effect, visual only).
    /// </summary>
    public void SendEmote(string emoteName)
    {
        string actionId = NewActionId();
        _mp.ActionSync.Competition("emote", emoteName, actionId);
    }

    // ─── Receive ──────────────────────────────────────────────────────────────────

    private void HandleCompetition(string json)
    {
        var data = MiniJson.Deserialize(json) as Dictionary<string, object>;
        if (data == null) return;

        string userId    = data.ContainsKey("user_id")     ? data["user_id"].ToString()     : "";
        string name      = data.ContainsKey("action_name") ? data["action_name"].ToString() : "";
        string msg       = data.ContainsKey("action_msg")  ? data["action_msg"].ToString()  : "";
        string actionId  = data.ContainsKey("action_id")   ? data["action_id"].ToString()   : "";
        long   timestamp = data.ContainsKey("timestamp")   ? Convert.ToInt64(data["timestamp"]) : 0L;

        // Guard: drop empty IDs (shouldn't happen with correct send code).
        if (string.IsNullOrEmpty(actionId))
        {
            Debug.LogWarning("[ActionSync] Received action with empty action_id — skipping");
            return;
        }

        // De-dup: skip if already processed.
        if (_seen.Contains(actionId)) return;
        _seen.Add(actionId);

        // Route by action name.
        switch (name)
        {
            case "attack":  ApplyRemoteAttack(userId, msg, timestamp);  break;
            case "ability": ApplyRemoteAbility(userId, msg, timestamp); break;
            case "emote":   PlayRemoteEmote(userId, msg);               break;
            default:
                Debug.Log($"[ActionSync] Unknown action '{name}' from {userId}");
                break;
        }
    }

    // ─── Apply effects ────────────────────────────────────────────────────────────

    private void ApplyRemoteAttack(string attackerId, string msgJson, long timestamp)
    {
        var payload = MiniJson.Deserialize(msgJson) as Dictionary<string, object>;
        if (payload == null) return;

        string targetId  = payload.ContainsKey("target") ? payload["target"].ToString() : "";
        int    damage    = payload.ContainsKey("dmg")    ? Convert.ToInt32(payload["dmg"]) : 0;
        string weapon    = payload.ContainsKey("weapon") ? payload["weapon"].ToString() : "";

        Debug.Log($"[ActionSync] {attackerId} attacked {targetId} for {damage} ({weapon})");

        // Apply VFX, play hit sound, reduce HP on the target if local.
        // Note: only apply to local-authority objects. Don't apply HP reduction
        // to objects the attacker owns — they'll be authoritative on their own side.
    }

    private void ApplyRemoteAbility(string userId, string msgJson, long timestamp)
    {
        var payload = MiniJson.Deserialize(msgJson) as Dictionary<string, object>;
        if (payload == null) return;

        string ability = payload.ContainsKey("ability") ? payload["ability"].ToString() : "";
        Debug.Log($"[ActionSync] {userId} used ability: {ability}");
        // Trigger ability animation on remote player representation.
    }

    private void PlayRemoteEmote(string userId, string emoteName)
    {
        Debug.Log($"[ActionSync] {userId} played emote: {emoteName}");
        // Play emote animation on remote player's Avatar.
    }

    // ─── Utilities ────────────────────────────────────────────────────────────────

    private static string NewActionId()
    {
        // First 8 chars of a GUID = 32 bits of randomness. Sufficient for in-session uniqueness.
        return Guid.NewGuid().ToString("N").Substring(0, 8);
    }

    // Call this after each match so the HashSet doesn't grow indefinitely.
    public void OnMatchEnd()
    {
        _seen.Clear();
        Debug.Log("[ActionSync] De-dup set cleared");
    }
}
```

## Payload Design Guide

| Scenario | action_name | action_msg example |
|----------|------------|-------------------|
| Melee attack | `"attack"` | `{"target":"p2","dmg":15,"weapon":"sword"}` |
| Ranged attack | `"attack"` | `{"target":"p3","dmg":8,"weapon":"bow","crit":true}` |
| Skill / ability | `"ability"` | `{"ability":"fireball","target":"p1"}` |
| AoE ability | `"ability"` | `{"ability":"shockwave","radius":5}` |
| Emote | `"emote"` | `"wave"` (plain string is fine) |
| Item use | `"item"` | `{"item":"health_potion","selfTarget":true}` |

## Notes

- The module type string is `"action_sync"` (underscore). The WebGL jslib path uses
  `actionsync/onCompetition` (no underscore) as the internal event name, but the
  JSON payload uses `"type":"action_sync"`.
- `timestamp` in the payload is set by the sender. Use it to detect stale actions
  in high-latency conditions (e.g., discard attacks older than 500ms).
- Keep `action_msg` to under 256 bytes when possible. Very large payloads increase
  data channel backpressure for all peers.
