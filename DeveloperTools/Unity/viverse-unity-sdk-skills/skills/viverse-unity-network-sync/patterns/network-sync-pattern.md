# NetworkSync Pattern — Continuous Transform Sync

## Overview

This pattern shows per-frame player position broadcasting with smooth interpolation
on the receiver side. The same approach scales to entity sync via `UpdateEntityPosition`.

## Architecture

```
Local Update() / FixedUpdate()
         |
         v (throttled to ~20 Hz)
NetworkSyncModule.UpdateMyPosition(json)
         |
         v (WebGL: jslib → TypeScript SDK → Mediasoup data channel)
         v (Editor: NativeWebSocket → proxy → data channel)
         |
         v (on all remote peers)
NetworkSyncModule.OnNotifyPositionUpdate fires
         |
         v
RemotePlayerController.SetTargetPosition(pos)
         |
         v (in Update, every frame)
transform.position = Vector3.Lerp(current, target, lerpSpeed * dt)
```

## RemotePlayer Helper

```csharp
using UnityEngine;

/// <summary>
/// Represents a remote player in the scene. Receives target positions
/// from network updates and interpolates toward them every frame.
/// </summary>
public class RemotePlayer : MonoBehaviour
{
    [SerializeField] private float lerpSpeed = 15f;

    private Vector3 _targetPosition;
    private Quaternion _targetRotation;
    private bool _hasTarget;

    public string UserId { get; set; }
    public long LastUpdateTimestamp { get; private set; }

    public void SetTargetPosition(Vector3 pos)
    {
        _targetPosition = pos;
        _hasTarget = true;
        LastUpdateTimestamp = System.DateTimeOffset.UtcNow.ToUnixTimeMilliseconds();
    }

    public void SetTargetRotation(Quaternion rot)
    {
        _targetRotation = rot;
    }

    private void Update()
    {
        if (!_hasTarget) return;
        transform.position = Vector3.Lerp(transform.position, _targetPosition,
            lerpSpeed * Time.deltaTime);
        transform.rotation = Quaternion.Slerp(transform.rotation, _targetRotation,
            lerpSpeed * Time.deltaTime);
    }
}
```

## PlayerSyncManager (Full Implementation)

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;
using ViverseSDK;

/// <summary>
/// Manages position sync for the local player (send) and all remote players (receive).
/// Wire this after MultiplayerClient.Init completes.
/// </summary>
public class PlayerSyncManager : MonoBehaviour
{
    [Header("References")]
    [SerializeField] private Transform _localPlayerTransform;
    [SerializeField] private GameObject _remotePlayerPrefab;

    [Header("Sync Config")]
    [SerializeField] private float _sendHz = 20f;

    private MultiplayerClient _mp;
    private Dictionary<string, RemotePlayer> _remoteMap = new Dictionary<string, RemotePlayer>();
    private float _syncTimer;

    // ─── Setup ────────────────────────────────────────────────────────────────────

    public void Initialize(MultiplayerClient mp)
    {
        _mp = mp;
        _syncTimer = 1f / _sendHz;

        // Subscribe BEFORE any sends.
        _mp.NetworkSync.OnNotifyPositionUpdate += HandlePositionUpdate;
        _mp.NetworkSync.OnNotifyRemove         += HandleRemoveEntity;

        // Clean up remote players when peers disconnect.
        _mp.OnClientDisconnected += RemoveRemotePlayer;

        Debug.Log("[NetworkSync] Manager initialized");
    }

    // ─── Send (local player) ──────────────────────────────────────────────────────

    private void Update()
    {
        if (_mp == null || _localPlayerTransform == null) return;

        _syncTimer -= Time.deltaTime;
        if (_syncTimer > 0f) return;
        _syncTimer = 1f / _sendHz;

        SendLocalPosition();
    }

    private void SendLocalPosition()
    {
        var pos = _localPlayerTransform.position;
        var rot = _localPlayerTransform.eulerAngles;

        // Keep payload compact — 3 decimal places is sufficient for world units.
        string json = $"{{" +
            $"\"x\":{pos.x:F3}," +
            $"\"y\":{pos.y:F3}," +
            $"\"z\":{pos.z:F3}," +
            $"\"ry\":{rot.y:F3}" +
            $"}}";

        _mp.NetworkSync.UpdateMyPosition(json);
    }

    // ─── Receive (remote players) ─────────────────────────────────────────────────

    private void HandlePositionUpdate(string json)
    {
        var data = MiniJson.Deserialize(json) as Dictionary<string, object>;
        if (data == null) return;

        string userId = data.ContainsKey("user_id") ? data["user_id"].ToString() : "";
        if (string.IsNullOrEmpty(userId)) return;

        // Separate entity updates from player updates.
        if (data.ContainsKey("entity_id"))
        {
            HandleEntityUpdate(data["entity_id"].ToString(), data);
            return;
        }

        // Player update
        var posData = data.ContainsKey("data") ? data["data"] as Dictionary<string, object> : null;
        if (posData == null) return;

        // Spawn remote player on first seen.
        if (!_remoteMap.TryGetValue(userId, out var remote))
        {
            var go = Instantiate(_remotePlayerPrefab);
            go.name = $"Remote_{userId.Substring(0, Mathf.Min(8, userId.Length))}";
            remote = go.GetComponent<RemotePlayer>();
            remote.UserId = userId;
            _remoteMap[userId] = remote;
            Debug.Log($"[NetworkSync] Spawned remote player: {userId}");
        }

        float x  = posData.ContainsKey("x")  ? Convert.ToSingle(posData["x"])  : 0f;
        float y  = posData.ContainsKey("y")  ? Convert.ToSingle(posData["y"])  : 0f;
        float z  = posData.ContainsKey("z")  ? Convert.ToSingle(posData["z"])  : 0f;
        float ry = posData.ContainsKey("ry") ? Convert.ToSingle(posData["ry"]) : 0f;

        remote.SetTargetPosition(new Vector3(x, y, z));
        remote.SetTargetRotation(Quaternion.Euler(0f, ry, 0f));
    }

    private void HandleEntityUpdate(string entityId, Dictionary<string, object> data)
    {
        // Extend for entity-specific handling (projectiles, vehicles, etc.)
        Debug.Log($"[NetworkSync] Entity update: {entityId}");
    }

    private void HandleRemoveEntity(string json)
    {
        var data = MiniJson.Deserialize(json) as Dictionary<string, object>;
        if (data == null) return;

        string entityId = data.ContainsKey("entity_id") ? data["entity_id"].ToString() : "";
        if (string.IsNullOrEmpty(entityId)) return;

        Debug.Log($"[NetworkSync] Remove entity: {entityId}");
        // Destroy the corresponding GameObject.
    }

    private void RemoveRemotePlayer(string userId)
    {
        if (_remoteMap.TryGetValue(userId, out var remote))
        {
            if (remote != null) Destroy(remote.gameObject);
            _remoteMap.Remove(userId);
            Debug.Log($"[NetworkSync] Removed remote player: {userId}");
        }
    }
}
```

## Send Rate vs Bandwidth Trade-offs

| Rate | Packets/sec (4 players) | Notes |
|------|------------------------|-------|
| 60 Hz | 240 | Never needed; wastes bandwidth |
| 30 Hz | 120 | Good for fast-paced shooters |
| 20 Hz | 80 | Default recommendation |
| 10 Hz | 40 | Fine for slow-paced games; noticeable lag |

Use `FixedUpdate` for physics-driven characters to align sync with physics steps.

## Notes

- The module type string is `"network_sync"` (underscore). The WebGL jslib path uses
  `networksync/onNotifyPositionUpdate` (no underscore) as the internal eventName,
  but `type:"network_sync"` in the JSON payload. Always set `ModuleType = "network_sync"`.
- Self-messages are filtered by the module before `OnNotifyPositionUpdate` fires.
  You don't need an extra `user_id != PeerId` guard in your handler.
- `timestamp` in the payload is set by the sender. Use it for dead-reckoning or
  to discard stale packets that arrive out of order.
