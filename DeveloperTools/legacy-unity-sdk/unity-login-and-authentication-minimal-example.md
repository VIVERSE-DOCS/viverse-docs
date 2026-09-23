---
description: >-
  Legacy VIVERSE Unity SDK v0.96 example. LoginManager is retired.
  Use AuthManager in the VIVERSE Unity SDK for C#.
---

# Unity Login example — legacy

This example documented VIVERSE Unity SDK v0.96. `LoginManager` is a legacy API and should not be used for new Unity projects.

## Current Unity implementation

**VIVERSE Unity SDK 1.2 · C# · `ViverseSDK`**

Use `AuthManager`.

```csharp
// VIVERSE Unity SDK 1.2 — Unity C#
using UnityEngine;
using ViverseSDK;

public class Bootstrap : MonoBehaviour
{
    void Start()
    {
        var auth = AuthManager.Instance;
        auth.OnLoginSuccess += result => Debug.Log($"Signed in as {result.account_id}");
        auth.OnError        += err    => Debug.LogError($"Auth error: {err}");
        auth.Initialize("YOUR_APP_ID");
    }

    public void OnLoginButtonClicked() => AuthManager.Instance.Login();
}
```

The login sample, including the `[AuthManager]` GameObject, is on the [VIVERSE Unity SDK](../viverse-unity-sdk.md) page.
