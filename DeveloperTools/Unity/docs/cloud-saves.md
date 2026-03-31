# Cloud saves

## Who this is for

Developers who want **server-side persistence** of per-user game data (levels, inventory, scores, arbitrary JSON) tied to a Viverse login.

**Package:** Viverse Unity SDK (e.g. `SDK_v0.96.unitypackage`).  
**Sample scene:** `Assets/Scenes/CloudSave_Sample.unity`

---

## What ships in the sample

- **`CloudSaveService`** — Loads and saves **cloud save** documents and exposes **UserApp**-style operations (latest, all versions, save, delete) using the authenticated user.  
- **`CloudSaveSample`** — Demo UI: load/save cloud data, get latest UserApp, list all, delete by version.

You must be **logged in** (`access_token` in `PlayerPrefs`) for the buttons that call protected APIs to succeed — see [Login](login.md).

---

## Quick how-to

1. Import Viverse Unity SDK; add **Newtonsoft.Json** if your Unity version does not include it.  
2. Open **`CloudSave_Sample.unity`**.  
3. Log in via **`Login_Sample`** or the same scene flow if combined.  
4. Use the sample buttons to **load** and **save**; watch the inspector / status text fields updated by `CloudSaveSample`.

Programmatic pattern (simplified):

```csharp
// After login — CloudSaveService uses LoginManager / token internally per implementation
cloudSaveService.LoadCloudSave();
cloudSaveService.SaveToCloud(myData);
cloudSaveService.GetUserAppLatest();
cloudSaveService.SaveUserAppData(level, score);
```

Refer to **`CloudSaveService`** event callbacks (`OnFullDataLoaded`, `OnSaveCompleted`, `OnUserAppLatestLoaded`, etc.) for UI updates.

---

## Relation to Play `WebRTCService`

The **Play** package includes **`WebRTCService`** under `Assets/Scripts/PlaySDK/Services/` with HTTP methods such as **`SaveUserApp`**, **`GetLatestUserApp`**, etc., against the **WebRTC bot service** base URL from `EnvConfig`. That is a **different integration path** (same broad idea: server-stored user app payloads) from the **Viverse SDK `CloudSaveService`** sample.

Choose one approach per product or bridge them deliberately; do not assume identical endpoints or auth headers without reading both implementations.

---

## See also

- [Login](login.md)  
- [Configuration](configuration.md)  
- [Troubleshooting](troubleshooting.md)  
