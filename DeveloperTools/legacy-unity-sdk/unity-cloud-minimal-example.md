---
description: >-
  Legacy VIVERSE Unity SDK v0.96 example. CloudSaveService is retired.
  Use CloudSaveClient in the VIVERSE Unity SDK for C#.
---

# Unity Cloud Save example — legacy

This example documented VIVERSE Unity SDK v0.96. `CloudSaveService` is a legacy API and should not be used for new Unity projects.

## Current Unity implementation

**VIVERSE Unity SDK 1.2 · C# · `ViverseSDK`**

Use `CloudSaveClient`.

```csharp
// VIVERSE Unity SDK 1.2 — Unity C#
using ViverseSDK;

var cloud = new CloudSaveClient("YOUR_APP_ID");
string token = AuthManager.Instance.AccessToken;

await cloud.Save("{\"level\":5,\"score\":1200}", token);
var latest = await cloud.GetLatest(token);
if (latest.success) Debug.Log($"Latest save: {latest.data}");
```

Authentication and cloud save samples are on the [VIVERSE Unity SDK](../viverse-unity-sdk.md) page.
