---
description: >-
  Add mid-roll and rewarded ads to a JavaScript web game on VIVERSE with the
  VIVERSE Ad SDK. Covers setup, cooldowns, pausing gameplay, and granting
  rewards.
---

# Javascript ADS

***

{% hint style="info" %}
For a Unity game, use the [Unity ads quickstart](unity-ads.md) instead. The Unity package loads this SDK for you.
{% endhint %}

## Before you start

The VIVERSE Ad SDK runs only on the VIVERSE platform. Ads don't display when you run your game locally, so build your game to keep working when the SDK fails to initialize.

You need:

* A [VIVERSE App ID](https://app.gitbook.com/s/4pMiThqqrBzfvP8uy5am/how-to-publish#option-2-create-an-experience-create-an-app-id) for your game.
* A game you can publish to VIVERSE for testing.

## Ad formats on this page

The SDK uses its own names for each format. Mid-roll ads are the Overlay and Video formats, and rewarded ads are the Reward Overlay and Reward Video formats.

| Format         | Type                                              | Who starts it             | Method                |
| -------------- | ------------------------------------------------- | ------------------------- | --------------------- |
| Overlay        | Mid-roll, full-screen interstitial                | Your game, between levels | `showOverlay()`       |
| Video          | Mid-roll, video interstitial                      | Your game, between levels | `showVideo()`         |
| Reward Overlay | Rewarded, full-screen, 15s video, image, or image | The player, from a button | `showRewardOverlay()` |
| Reward Video   | Rewarded, 60s video                               | The player, from a button | `showRewardVideo()`   |

The SDK also supports banner ads, which this page doesn't cover.

## Set up the SDK

Load the SDK script, then initialize it once before you call any `show` method.

{% stepper %}
{% step %}
### Add the SDK script

Add the versioned SDK script to your HTML page. Use a versioned URL in production so an SDK update can't change your game's behavior without warning.

```html
<script src="https://www.viverse.com/static-assets/viverse-ad-sdk/0.0.6/viverse-ad-sdk.umd.js"></script>
```

In the PlayCanvas Editor, open your project **Settings**, find **External Scripts**, and add the same URL.

<details>

<summary>Load the script at runtime instead</summary>

If you load the SDK conditionally, wait for the script's `onload` event before you use it. Until then, `window.ViverseAdSDK` is undefined.

```javascript
function loadViverseAdSDK() {
  return new Promise((resolve, reject) => {
    const script = document.createElement('script');
    script.src = 'https://www.viverse.com/static-assets/viverse-ad-sdk/0.0.6/viverse-ad-sdk.umd.js';
    script.onload = resolve;
    script.onerror = reject;
    document.head.appendChild(script);
  });
}

await loadViverseAdSDK();
```

</details>
{% endstep %}

{% step %}
### Initialize the SDK

The SDK is a global singleton at `window.ViverseAdSDK`. Call `init()` once and wait for it to finish before you show any ad.

```javascript
const viverseAds = window.ViverseAdSDK;
let adsReady = false;

try {
  await viverseAds.init({ appId: 'YOUR_APP_ID' });
  adsReady = true;
} catch (error) {
  // Ads are unavailable. Keep the game playable without them.
  console.error('VIVERSE Ad SDK initialization failed:', error);
}
```
{% endstep %}
{% endstepper %}

## Pause and resume your game during an ad

Every format on this page covers the screen, so your game must stop while the ad plays. The SDK tells you when to pause and when to resume through callbacks.

| Callback       | What your game does                                                     |
| -------------- | ----------------------------------------------------------------------- |
| `onBreakStart` | Pause gameplay, animations, input, background music, and sound effects. |
| `onBreakEnd`   | Resume everything you paused.                                           |
| `onAdError`    | Resume everything you paused, and let the game continue.                |

Resume in both `onBreakEnd` and `onAdError`. When no ad is available, the SDK calls `onAdError` instead of showing an ad, and `onAdError` can fire before `onBreakStart`. Make your pause and resume functions safe to call more than once.

```javascript
let paused = false;

function pauseForAd() {
  if (paused) return;
  paused = true;
  pauseGame();   // your game: stop the loop, input, and audio
}

function resumeAfterAd() {
  if (!paused) return;
  paused = false;
  resumeGame();  // your game: restart the loop, input, and audio
}
```

{% hint style="warning" %}
Never block gameplay because an ad failed. Ad blockers, network errors, and empty ad inventory all arrive as `onAdError`. Your game must continue normally in every case.
{% endhint %}

## Show mid-roll ads between levels

Show Overlay or Video ads at natural breaks, such as after a level ends and before the next one loads. Don't show one when the game first starts, before the player has played anything.

Overlay and Video share one cooldown. Wait at least 30 seconds after either one before you call either one again. Track the cooldown in your game; the example below uses a single shared timer.

```javascript
const MIDROLL_COOLDOWN_MS = 30000; // shared by Overlay and Video
let lastMidrollAt = 0;
let adInProgress = false;

function showMidrollAd({ video = false } = {}) {
  if (!adsReady || adInProgress) return;

  const now = Date.now();
  if (now - lastMidrollAt < MIDROLL_COOLDOWN_MS) return;
  lastMidrollAt = now;
  adInProgress = true;

  const callbacks = {
    onBreakStart: () => pauseForAd(),
    onBreakEnd: () => {
      adInProgress = false;
      resumeAfterAd();
    },
    onAdError: (error) => {
      adInProgress = false;
      resumeAfterAd();
      console.warn('Mid-roll ad not shown:', error);
    },
  };

  try {
    if (video) {
      viverseAds.showVideo(callbacks);
    } else {
      viverseAds.showOverlay(callbacks);
    }
  } catch (error) {
    adInProgress = false;
    resumeAfterAd();
    console.warn('Mid-roll ad not shown:', error);
  }
}

// Example: show a mid-roll ad between levels
function onLevelComplete() {
  showMidrollAd();
  loadNextLevel();
}
```

`showVideo()` passes a `skipped` boolean to `onBreakEnd` if you want to log whether the player skipped the video.

## Show rewarded ads

A rewarded ad gives the player something in exchange for watching, such as an extra life or in-game currency. The player must start it. Show it only from a button or similar control the player chooses, such as **Watch an ad to continue**, and never autoplay it.

Grant the reward only inside `onRewardGranted`. Don't grant it in `onBreakEnd`.

```javascript
function showRewardedAd(grantReward, { video = true } = {}) {
  if (!adsReady || adInProgress) return;
  adInProgress = true;
  let rewarded = false;

  const callbacks = {
    onBreakStart: () => pauseForAd(),
    onRewardGranted: () => {
      if (rewarded) return; // grant once per ad
      rewarded = true;
      grantReward();
    },
    onBreakEnd: () => {
      adInProgress = false;
      resumeAfterAd();
    },
    onAdError: (error) => {
      adInProgress = false;
      resumeAfterAd();
      showMessage('No ad is available right now. Try again later.');
      console.warn('Rewarded ad not shown:', error);
    },
  };

  try {
    if (video) {
      viverseAds.showRewardVideo(callbacks);
    } else {
      viverseAds.showRewardOverlay(callbacks);
    }
  } catch (error) {
    adInProgress = false;
    resumeAfterAd();
    console.warn('Rewarded ad not shown:', error);
  }
}

// Example: wire the reward to a Game Over button
document.getElementById('watch-ad-to-revive').addEventListener('click', () => {
  showRewardedAd(() => revivePlayer());
});
```

{% hint style="info" %}
**When `onRewardGranted` fires**

For Reward Video, the SDK currently calls `onRewardGranted` when the ad starts playing, not when the video finishes.

Players with VIVERSE AD FREE receive the reward without watching an ad. `onRewardGranted` still fires for them, so always grant rewards from that callback.
{% endhint %}

## Test your integration

Ads appear only when your game runs on VIVERSE. Publish a build to VIVERSE, then check each item below.

* Mid-roll ads appear between levels and never at game start.
* A second mid-roll ad within 30 seconds of the first one is skipped.
* Gameplay, input, music, and sound effects stop during every ad and resume afterward.
* The game keeps running when an ad fails. To test this, turn on an ad blocker.
* Rewarded ads start only from a player action, and each ad grants its reward once.

## API reference

These are the fullscreen methods and callbacks this page uses. Call every method on `window.ViverseAdSDK` after `init()` resolves. Only one ad can show at a time.

| Method                       | Callbacks                                                                        | Returns           |
| ---------------------------- | -------------------------------------------------------------------------------- | ----------------- |
| `init({ appId })`            | None. Returns a promise.                                                         | `Promise`         |
| `showOverlay(options)`       | `onAdReady`, `onBreakStart`, `onBreakEnd`, `onAdError(error)`                    | `OverlayAd`       |
| `showVideo(options)`         | `onBreakStart`, `onBreakEnd(skipped)`, `onAdError(error)`                        | `VideoAd`         |
| `showRewardOverlay(options)` | `onAdReady`, `onBreakStart`, `onRewardGranted`, `onBreakEnd`, `onAdError(error)` | `RewardOverlayAd` |
| `showRewardVideo(options)`   | `onBreakStart`, `onRewardGranted`, `onBreakEnd(skipped)`, `onAdError(error)`     | `RewardVideoAd`   |

All callbacks are optional. `onAdReady` fires when the ad has loaded, just before it displays.
