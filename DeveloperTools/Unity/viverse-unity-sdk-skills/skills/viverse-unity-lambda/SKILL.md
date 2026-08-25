---
name: viverse-unity-lambda
description: VIVERSE Unity SDK Lambda — Invoke registered server-side functions via REST API with dual-path architecture.
prerequisites: [Unity 2021.2+, VIVERSE App ID, Auth token, Registered Lambda event]
tags: [unity, lambda, serverless, viverse-sdk, webgl, editor, rest, play-sdk]
---

# VIVERSE Unity SDK — Lambda (Serverless Functions)

Invoke server-side Lambda functions from Unity. Pure REST — no WebRTC or multiplayer connection required.

## When To Use This Skill

Use when a Unity project needs:
- Server-side computation (game logic, validation, AI processing)
- Secure operations that shouldn't run on client
- Any registered Lambda event invocation with JSON input/output

## Architecture Overview

Lambda is **pure REST** — completely independent of multiplayer/matchmaking:

```
┌──────────────────────────────────────────────────┐
│  LambdaClient.cs (thin interface)                │
│  ├── WebGL: jslib fetch() → BCGW REST API        │
│  └── Editor: UnityWebRequest → BCGW REST API     │
└──────────────────────────────────────────────────┘
         │
         ▼  POST /api/play-lambda-service/v1/jobs
┌──────────────────────────────────────────────────┐
│  Broadcasting Gateway (BCGW)                      │
│  https://broadcasting-gateway-gaming.vrprod...    │
└──────────────────────────────────────────────────┘
         │
         ▼  Execute registered handler
┌──────────────────────────────────────────────────┐
│  Lambda Runtime (server-side script)             │
└──────────────────────────────────────────────────┘
```

## Read Order

1. This file (API + workflow)
2. [patterns/dual-path-lambda.md](patterns/dual-path-lambda.md)

## Prerequisites

1. Unity 2021.2+ with .NET 4.x runtime
2. App ID from [VIVERSE Studio](https://studio.viverse.com/)
3. Valid access token (user must be logged in via AuthManager)
4. Lambda event registered on server (see Develop_ReadMe.md for registration)

## SDK File Structure

```
Assets/viverse-unity-sdk/
├── Runtime/LambdaClient.cs              # Thin C# interface (standalone, async)
├── Plugins/JSLib/ViverseLambda.jslib    # WebGL bridge (fetch-based, polling)
└── Sample/ViverseTestRunner.cs          # Test UI with Lambda section
```

## Critical Implementation Rules

### Auth Is Required First

Lambda requires a valid (non-expired) access token. Always verify login before invoking:

```csharp
if (!AuthManager.Instance.IsLoggedIn)
{
    Debug.LogError("Must login before invoking Lambda");
    return;
}
```

### Token Header Format

VIVERSE APIs do NOT use `Authorization: Bearer`. They use custom headers:

```
JWT token (3 dot-separated segments) → AccessToken: <jwt>
Non-JWT (AuthKey)                    → AuthKey: <key>
```

### Response Is Nested

Server responses nest data under a `job` key:

```json
{
  "success": true,
  "job": {
    "job_id": "job_abc123",
    "status": "succeeded",
    "result": [{"Key": "echo", "Value": [...]}]
  }
}
```

Always access `response.job.status`, NOT `response.status`.

### Result Format (Key/Value Array)

Lambda results use a Key/Value array format:

```json
[
  {"Key": "echo", "Value": [{"Key": "message", "Value": "hello"}]},
  {"Key": "success", "Value": true},
  {"Key": "timestamp", "Value": 1780301496906}
]
```

### No MultiplayerClient Dependency

LambdaClient is standalone. Never import or require MultiplayerClient, data channel, or WebRTC connection. Lambda is pure HTTP.

## Public API

```csharp
namespace ViverseSDK
{
    public class LambdaClient
    {
        public const int DefaultPollIntervalMs = 1000;
        public const int DefaultPollTimeoutMs = 60000;

        public LambdaClient(string appId);

        /// <summary>
        /// Invoke a Lambda function. Creates job, polls until terminal state.
        /// </summary>
        public async Task<LambdaResult> Invoke(
            string eventName,
            string dataJson,
            string token,
            CancellationToken ct = default);
    }

    [Serializable]
    public class LambdaResult
    {
        public bool success;
        public string status;   // "succeeded", "failed", "timeout"
        public string result;   // JSON string of result array
        public string error;    // Error message if failed
    }
}
```

## Integration Example

```csharp
using ViverseSDK;
using UnityEngine;

public class MyGame : MonoBehaviour
{
    private LambdaClient _lambda;

    void Start()
    {
        _lambda = new LambdaClient("YOUR_APP_ID");

        AuthManager.Instance.OnLoginSuccess += async (result) =>
        {
            // Invoke after login
            var lambdaResult = await _lambda.Invoke(
                "my_event_name",
                "{\"key\": \"value\"}",
                AuthManager.Instance.AccessToken
            );

            if (lambdaResult.success)
                Debug.Log($"Result: {lambdaResult.result}");
            else
                Debug.LogError($"Lambda failed: {lambdaResult.error}");
        };
    }
}
```

## Lambda Lifecycle

1. **Create Job**: POST to `/api/play-lambda-service/v1/jobs` with `{game_id, event_name, data, request_id}`
2. **Server Returns**: `{success: true, job_id: "job_xxx", status: "pending"}`
3. **Poll**: GET `/api/play-lambda-service/v1/jobs/{jobId}` every 1s
4. **Terminal States**: `succeeded` | `failed` | `timeout`
5. **Result**: Available in `job.result` when succeeded

## WebGL Callback Bridge

WebGL path uses `SendMessage` to bridge jslib → C#:

1. `LambdaClient.Invoke()` → registers `TaskCompletionSource` in `LambdaCallbackRegistry`
2. jslib `ViverseLambda_CreateJob()` → fetch + poll → `SendMessage(gameObject, "OnLambdaCallback", json)`
3. `AuthManager.OnLambdaCallback()` → extracts `bridgeId` → resolves/rejects the TCS
4. `LambdaClient` awaits TCS → returns `LambdaResult`

## Compliance Gates

Before marking Lambda implementation complete:
- [ ] Editor: Login → Invoke known event → result appears in UI
- [ ] WebGL: Login → Invoke known event → result appears in UI
- [ ] Expired token returns clear error message (not generic "no job_id")
- [ ] Invalid event name returns server error (not hang/timeout)
- [ ] CancellationToken support works (Editor path)
- [ ] No dependency on MultiplayerClient anywhere in code
