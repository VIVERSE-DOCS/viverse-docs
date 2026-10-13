---
description: >-
  Add mid-roll and rewarded ads to a Unity WebGL game on VIVERSE with the
  VIVERSE Ad SDK Unity package. Covers setup, cooldowns, pausing, and rewards.
hidden: true
---

# Quickstart: Add mid-roll and rewarded ads to a Unity game

Use the VIVERSE Ad SDK Unity package to show mid-roll ads between levels and rewarded ads that players choose to watch in a Unity WebGL game.

***

{% hint style="info" %}
**The Ad SDK is a separate package**

Ads are not part of the [VIVERSE Unity SDK](viverse-unity-sdk.md). Import the VIVERSE Ad SDK Unity package alongside it. The package includes its own WebGL bridge, so you don't add a script tag to your WebGL template.
{% endhint %}

## Before you start

The VIVERSE Ad SDK runs only on the VIVERSE platform. In the Unity Editor, the package simulates every ad so you can test your game logic, but real ads appear only in a WebGL build published to VIVERSE.

You need:

* A Unity project with the **WebGL** build target.
* A VIVERSE App ID for your game.
* The VIVERSE Ad SDK Unity package, `VADK_v0.0.6.unitypackage`.

## Ad formats on this page

The SDK uses its own names for each format. Mid-roll ads are the Overlay and Video formats, and rewarded ads are the Reward Overlay and Reward Video formats.

| Format | Type | Who starts it | Method |
| --- | --- | --- | --- |
| Overlay | Mid-roll, full-screen interstitial | Your game, between levels | `ShowOverlayAsync()` |
| Video | Mid-roll, video interstitial | Your game, between levels | `ShowVideoAsync()` |
| Reward Overlay | Rewarded, full-screen (mostly video) | The player, from a button | `ShowRewardOverlayAsync()` |
| Reward Video | Rewarded, video | The player, from a button | `ShowRewardVideoAsync()` |

The package also includes `ViverseAdsBannerClient` for banner ads, which this page doesn't cover.

## Set up the package

Import the package, add the two client components to your first scene, and initialize the SDK once at startup.

{% stepper %}
{% step %}
### Import the package

In Unity, open **Assets > Import Package > Custom Package** and select `VADK_v0.0.6.unitypackage`. The scripts use the `ViverseAdSDK` namespace.

The package includes a sample scene, `ViverseAdFullscreenButtonTestScene`, with a button for each ad format. Its test UI uses the Input System package. If your project doesn't use the Input System, clear the `Assets/Scenes` folder and `ViverseAdFullscreenTestUIBuilder.cs` in the import dialog.
{% endstep %}

{% step %}
### Add the client components

In your first scene, create two empty GameObjects named `ViverseAdsClient` and `ViverseAdsFullscreenClient`. Add the component with the same name to each one.

Both components persist across scene loads. Give each GameObject a name that is unique in your scenes, because the WebGL bridge sends callbacks to it by name.
{% endstep %}

{% step %}
### Initialize the SDK

Call `InitAsync` once at startup, before you show any ad. It returns `true` when the SDK is ready.

```csharp
// VIVERSE Ad SDK 0.0.6 — Unity C#
using UnityEngine;
using ViverseAdSDK;

public class AdsBootstrap : MonoBehaviour
{
    [SerializeField] private string appId = "YOUR_APP_ID";

    private async void Start()
    {
        bool ready = await ViverseAdsClient.Instance.InitAsync(appId);
        if (!ready)
        {
            // Ads are unavailable. Keep the game playable without them.
            Debug.LogWarning("VIVERSE Ad SDK initialization failed.");
        }
    }
}
```

Check `ViverseAdsClient.Instance.IsInitialized` before each ad request, or subscribe to the `OnInitialized` and `OnInitializeFailed` events.
{% endstep %}
{% endstepper %}

## Pause and resume your game during an ad

Every format on this page covers the screen, so your game must stop while the ad plays. `ViverseAdsFullscreenClient` raises events that tell you when to pause and when to resume.

| Event | What your game does |
| --- | --- |
| `OnBreakStart` | Pause gameplay, animations, input, background music, and sound effects. |
| `OnBreakEnd` | Resume everything you paused. |
| `OnError` | Resume everything you paused, and let the game continue. |

Resume in both `OnBreakEnd` and `OnError`. When no ad is available, `OnError` fires instead of `OnBreakStart`. The task returned by each `Show...Async` method completes when the ad is ready or starts, not when it ends, so use the events to resume your game.

The `AdBreakManager` component below handles pausing, cooldowns, and rewards for the rest of this page. It pauses with `Time.timeScale` and `AudioListener.pause`; replace those with your game's own pause logic if you have it.

```csharp
// VIVERSE Ad SDK 0.0.6 — Unity C#
using System;
using UnityEngine;
using ViverseAdSDK;

public class AdBreakManager : MonoBehaviour
{
    private const float MidrollCooldownSeconds = 30f; // shared by Overlay and Video

    private float _lastMidrollTime = float.NegativeInfinity;
    private bool _adInProgress;
    private bool _paused;
    private float _timeScaleBeforeAd = 1f;
    private Action _pendingReward;

    private void Start()
    {
        var ads = ViverseAdsFullscreenClient.Instance;
        ads.OnBreakStart    += HandleBreakStart;
        ads.OnBreakEnd      += HandleBreakEnd;
        ads.OnError         += HandleError;
        ads.OnRewardGranted += HandleRewardGranted;
    }

    private void OnDestroy()
    {
        var ads = ViverseAdsFullscreenClient.Instance;
        if (ads == null) return;
        ads.OnBreakStart    -= HandleBreakStart;
        ads.OnBreakEnd      -= HandleBreakEnd;
        ads.OnError         -= HandleError;
        ads.OnRewardGranted -= HandleRewardGranted;
    }

    private bool AdsReady =>
        ViverseAdsClient.Instance != null && ViverseAdsClient.Instance.IsInitialized;

    private void HandleBreakStart(FullscreenAdHandle handle) => PauseForAd();

    private void HandleBreakEnd(FullscreenAdHandle handle, bool skipped)
    {
        _adInProgress = false;
        ResumeAfterAd();
    }

    private void HandleError(FullscreenAdHandle handle, string message)
    {
        _adInProgress = false;
        _pendingReward = null;
        ResumeAfterAd();
        Debug.LogWarning($"Ad not shown ({handle.Format}): {message}");
    }

    // Safe to call more than once.
    private void PauseForAd()
    {
        if (_paused) return;
        _paused = true;
        _timeScaleBeforeAd = Time.timeScale;
        Time.timeScale = 0f;
        AudioListener.pause = true;
    }

    private void ResumeAfterAd()
    {
        if (!_paused) return;
        _paused = false;
        Time.timeScale = _timeScaleBeforeAd;
        AudioListener.pause = false;
    }

    // Mid-roll and rewarded methods follow in the next sections.
}
```

{% hint style="warning" %}
Never block gameplay because an ad failed. Ad blockers, network errors, and empty ad inventory all arrive as `OnError`. Your game must continue normally in every case.
{% endhint %}

## Show mid-roll ads between levels

Show Overlay or Video ads at natural breaks, such as after a level ends and before the next one loads. Don't show one when the game first starts, before the player has played anything.

Overlay and Video share one cooldown. Wait at least 30 seconds after either one before you call either one again. Add this method to `AdBreakManager`. It uses `Time.realtimeSinceStartup` so the cooldown keeps counting while `Time.timeScale` is `0`.

```csharp
public async void ShowMidrollAd(bool video = false)
{
    if (!AdsReady || _adInProgress) return;
    if (Time.realtimeSinceStartup - _lastMidrollTime < MidrollCooldownSeconds) return;

    _lastMidrollTime = Time.realtimeSinceStartup;
    _adInProgress = true;

    try
    {
        if (video)
            await ViverseAdsFullscreenClient.Instance.ShowVideoAsync();
        else
            await ViverseAdsFullscreenClient.Instance.ShowOverlayAsync();
    }
    catch (Exception ex)
    {
        // OnError has already resumed the game.
        Debug.LogWarning($"Mid-roll ad not shown: {ex.Message}");
    }
}
```

Call it from your level flow, for example `adBreakManager.ShowMidrollAd();` when a level is complete. `OnBreakEnd` passes a `skipped` value for Video if you want to log whether the player skipped.

## Show rewarded ads

A rewarded ad gives the player something in exchange for watching, such as an extra life or in-game currency. The player must start it. Call it only from a button or similar control the player chooses, such as **Watch an ad to continue**, and never autoplay it.

Grant the reward only in `OnRewardGranted`. Don't grant it in `OnBreakEnd`. Add these methods to `AdBreakManager`.

```csharp
public async void ShowRewardedAd(Action grantReward, bool video = true)
{
    if (!AdsReady || _adInProgress) return;

    _adInProgress = true;
    _pendingReward = grantReward;

    try
    {
        if (video)
            await ViverseAdsFullscreenClient.Instance.ShowRewardVideoAsync();
        else
            await ViverseAdsFullscreenClient.Instance.ShowRewardOverlayAsync();
    }
    catch (Exception ex)
    {
        // OnError has already resumed the game and cleared the reward.
        Debug.LogWarning($"Rewarded ad not shown: {ex.Message}");
    }
}

private void HandleRewardGranted(FullscreenAdHandle handle, float amount, string type)
{
    if (handle.Format != FullscreenAdFormat.RewardOverlay &&
        handle.Format != FullscreenAdFormat.RewardVideo) return;

    var grant = _pendingReward;
    _pendingReward = null; // grant once per ad
    grant?.Invoke();
}
```

Connect it to a UI button. For example, on a Game Over screen:

```csharp
reviveButton.onClick.AddListener(() =>
    adBreakManager.ShowRewardedAd(() => player.Revive()));
```

Decide what the reward is in your own game code. Don't rely on the `amount` and `type` arguments of `OnRewardGranted` to set its value.

{% hint style="info" %}
**When `OnRewardGranted` fires**

For Reward Video, the SDK currently raises `OnRewardGranted` when the ad starts playing, not when the video finishes.

Players with VIVERSE AD FREE receive the reward without watching an ad. `OnRewardGranted` still fires for them, so always grant rewards from that event.
{% endhint %}

## Test your integration

Test in two places. The Editor confirms your game logic, and a published WebGL build confirms real ads.

In the Unity Editor and other non-WebGL targets, `InitAsync` always succeeds and each `Show...Async` call simulates a complete ad: ready, break start, a reward for rewarded formats, then break end. No ad appears on screen. Use Play mode to check that your game pauses, resumes, and grants rewards in the right places.

To see real ads, build for WebGL and [publish your game to VIVERSE](https://docs.viverse.com/standalone-app-publishing/unitywebgl-examples). Then check each item below.

* Mid-roll ads appear between levels and never at game start.
* A second mid-roll ad within 30 seconds of the first one is skipped.
* Gameplay, input, music, and sound effects stop during every ad and resume afterward.
* The game keeps running when an ad fails. To test this, turn on an ad blocker.
* Rewarded ads start only from a player action, and each ad grants its reward once.

## API reference

These are the members this page uses. All of them are in the `ViverseAdSDK` namespace. Only one ad can show at a time.

### ViverseAdsClient

| Member | Description |
| --- | --- |
| `Instance` | The singleton component in your scene. |
| `InitAsync(string appId)` | Initializes the SDK. Returns `Task<bool>`: `true` on success. |
| `IsInitialized` | `true` after `InitAsync` succeeds. |
| `OnInitialized` | Raised when initialization succeeds. |
| `OnInitializeFailed` | Raised with an error message when initialization fails. |

### ViverseAdsFullscreenClient

| Member | Description |
| --- | --- |
| `Instance` | The singleton component in your scene. |
| `ShowOverlayAsync()` | Requests an Overlay ad. Returns `Task<FullscreenAdHandle>`. |
| `ShowVideoAsync()` | Requests a Video ad. Returns `Task<FullscreenAdHandle>`. |
| `ShowRewardOverlayAsync()` | Requests a Reward Overlay ad. Returns `Task<FullscreenAdHandle>`. |
| `ShowRewardVideoAsync()` | Requests a Reward Video ad. Returns `Task<FullscreenAdHandle>`. |
| `OnReady(FullscreenAdHandle)` | Raised when the ad has loaded. |
| `OnBreakStart(FullscreenAdHandle)` | Raised when the ad starts. Pause your game. |
| `OnBreakEnd(FullscreenAdHandle, bool skipped)` | Raised when the ad ends. Resume your game. |
| `OnRewardGranted(FullscreenAdHandle, float amount, string type)` | Raised when the player earns the reward. Grant it here. |
| `OnError(FullscreenAdHandle, string message)` | Raised when the ad fails or isn't available. Resume your game. |

Each `Show...Async` task completes when the ad is ready or starts, and faults with an exception when the request fails. `FullscreenAdHandle` has a `RequestId` and a `Format` (`Overlay`, `Video`, `RewardOverlay`, or `RewardVideo`). Events fire for every request, so check `handle.Format` or `handle.RequestId` when you need to tell ads apart.
