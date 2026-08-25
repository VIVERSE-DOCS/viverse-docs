# Dual-Path Lambda Architecture

## Overview

LambdaClient uses conditional compilation to select the correct HTTP transport:

```
┌─────────────────────────────────────────────────────┐
│  LambdaClient.Invoke(eventName, dataJson, token)    │
├─────────────────────────────────────────────────────┤
│  #if UNITY_WEBGL && !UNITY_EDITOR                   │
│  ┌───────────────────────────────────────────────┐  │
│  │  InvokeWebGL()                                │  │
│  │  1. Register TaskCompletionSource in registry │  │
│  │  2. Call jslib ViverseLambda_CreateJob()       │  │
│  │  3. jslib: fetch POST → poll GET → SendMessage│  │
│  │  4. AuthManager.OnLambdaCallback → resolve TCS│  │
│  └───────────────────────────────────────────────┘  │
│  #else                                              │
│  ┌───────────────────────────────────────────────┐  │
│  │  InvokeEditor()                               │  │
│  │  1. UnityWebRequest POST (create job)         │  │
│  │  2. Parse job_id from nested response         │  │
│  │  3. Poll with UnityWebRequest GET             │  │
│  │  4. Return LambdaResult on terminal state     │  │
│  └───────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────┘
```

## WebGL Path Details

The jslib handles ALL logic (create + poll loop):

```javascript
// ViverseLambda.jslib
ViverseLambda_CreateJob: function(bridgeId, gameObjectName, callbackMethod, gameId, eventName, dataJson, requestId, token) {
    // 1. Parse dataJson (handle escaped quotes from Unity serialization)
    // 2. POST to /api/play-lambda-service/v1/jobs
    // 3. Extract job_id from response.job_id or response.job.job_id
    // 4. Poll GET /jobs/{jobId} every 1s until terminal
    // 5. SendMessage back to Unity with result JSON
}
```

Callback bridge flow:
1. C# registers `TaskCompletionSource<string>` → gets `bridgeId` (int)
2. jslib receives `bridgeId` as first param
3. On completion, jslib sends JSON with `bridgeId` field via `SendMessage`
4. `AuthManager.OnLambdaCallback` extracts bridgeId → resolves/rejects the matching TCS

## Editor Path Details

Pure C# using UnityWebRequest:

```csharp
// 1. Build JSON body manually (no serializer dependency)
string body = $"{{\"game_id\":\"{appId}\",\"event_name\":\"{eventName}\",\"data\":{dataJson},\"request_id\":\"{requestId}\"}}";

// 2. POST with token header
var req = new UnityWebRequest(url, "POST");
req.SetRequestHeader("AccessToken", token);  // JWT detection

// 3. Poll loop with CancellationToken support
while (not_timed_out) {
    await Task.Delay(1000, ct);
    var status = await Get(pollUrl, token);
    // Check nested: response.job.status
}
```

## Key Gotchas

### 1. Escaped Quotes from Unity

Unity's TextField/InputField can produce `{\"key\":\"value\"}` instead of `{"key":"value"}`. Both paths clean this:

```csharp
string cleanData = (dataJson ?? "{}").Replace("\\\"", "\"");
```

### 2. Nested Response Parsing

The BCGW server wraps everything in a `job` object. Both paths must unwrap:

```
// Create response: job_id at root OR under job
response.job_id || response.job.job_id

// Poll response: status and result under job
response.job.status
response.job.result
```

### 3. bridgeId Int32/Int64 Cast

MiniJson deserializes numbers inconsistently across platforms. Use `Convert.ToInt32()`:

```csharp
// WRONG: fails on some platforms
int bridgeId = (int)(long)dict["bridgeId"];

// CORRECT: handles both Int32 and Int64
int bridgeId = Convert.ToInt32(dict["bridgeId"]);
```
