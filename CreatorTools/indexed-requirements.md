---
description: >-
  What your game needs to pass VIVERSE review and become indexed: minimum frame
  rate, no experience-breaking bugs, no malware, and content policy compliance.
hidden: true
---

# Requirements for indexed content

To move from staging to indexed, your content must pass a review of four areas: performance, bugs, security, and content policy. This page lists each requirement, how VIVERSE tests it, and what to check before you select **Submit for Review**.

***

{% hint style="info" %}
Indexed review usually takes 24 to 72 hours. Every update to indexed or curated content goes through this same review before it goes live. For an overview of all three content states, see [How content review works on VIVERSE](README.md).
{% endhint %}

## Requirements at a glance

Your content must meet all four requirements on every device type you declare in VIVERSE Studio.

| Area | Requirement |
| --- | --- |
| Performance | Average frame rate of at least 24 FPS on the minimum-spec reference device for each device type you declare |
| Bugs | No unresolved P0 or P1 issues |
| Security | No malware or prohibited technical behavior |
| Content policy | No prohibited content, and an accurate age rating |

## Performance

VIVERSE plays representative parts of your content on the reference devices below and measures the average frame rate over a continuous session. The indexed floor is an average of **24 FPS** on the minimum-spec device for each device type you declare.

| Device type | Reference device | OS and browsers | Input |
| --- | --- | --- | --- |
| Desktop | Windows PC at 1080p | Windows 11, latest Chrome, Edge, and Firefox | Keyboard and mouse |
| Mobile (iOS) | iPhone 15 | iOS 17 or later, Safari and Chrome | Touch, portrait and landscape |
| Mobile (Android, high-end) | Google Pixel 8 Pro or Samsung Galaxy S25 | Android 14 or later, Chrome and Samsung Internet | Touch |
| Mobile (Android, minimum spec) | Samsung Galaxy A16 | Android 14 or later, Chrome | Touch |
| VR and XR | Meta Quest 3 | Horizon OS, Meta Quest Browser (WebXR) | 6DoF controllers or hand tracking |

Meeting the average isn't enough on its own. Sustained drops, severe stutter, input lag, or overheating that break the experience can still fail review.

Your content must also load. A persistent blank screen or a loading state that never finishes is a P0 issue. Show a loading indicator when loading takes longer than three seconds.

### When some devices fail

If your content fails on some declared devices but passes on others, it can still be indexed. VIVERSE handles device issues this way:

* **Minor issues:** If UI, text, or graphics are slightly misaligned or obscured but don't block the core experience, your content passes for that device.
* **Blocking issues:** If an issue blocks the core experience on a device, VIVERSE removes that device from your supported list and tells you. Your content is indexed for the devices that passed.
* **Restoring a device:** You don't need to do anything if you accept the updated device list. To restore support for a removed device, fix the issue and submit an updated build.

## Bugs

VIVERSE classifies every issue found during review using the severity levels below. To be indexed, your content must have **no unresolved P0 or P1 issues** on any declared device.

| Severity | What it means | Examples | Indexed | Curated |
| --- | --- | --- | --- | --- |
| **P0: System blocker** | Prevents launch, or causes severe platform, security, hardware, or data impact | Crash on launch, data loss, security compromise, device instability | Not allowed | Not allowed |
| **P1: Core interaction blocker** | Content launches, but the main interaction or completion is impossible | Main controls fail, login loop, core scene can't load | Not allowed | Not allowed |
| **P2: Major** | A major feature is impaired, but the content is still usable | An important secondary feature fails, severe visual or audio defect | Allowed | Should be rare |
| **P3: Minor** | Limited impact that doesn't block normal use | Minor alignment, localization, or cosmetic issues | Allowed | Allowed in small numbers |

When your content doesn't pass, the review result lists the issues that blocked it.

## Security

VIVERSE runs a basic malware scan on every upload and a more in-depth scan during indexed review. Your content must not contain or enable:

* Malware, backdoors, credential harvesting, cryptomining, or unauthorized remote code execution.
* Code that hides prohibited behavior from review, or changes the reviewed behavior after review.
* Undisclosed debug tools, admin interfaces, or remote diagnostics.
* Unauthorized access to VIVERSE systems, other content, player accounts, or device resources.
* Insecure transmission of credentials, tokens, or personal data.

Analytics, crash reporting, and logging are allowed when they're disclosed, secure, and comply with privacy requirements.

{% hint style="danger" %}
Content with malicious or deceptive technical behavior is rejected immediately. Serious or repeated violations may lead to suspension of your VIVERSE account.
{% endhint %}

## Content policy and age rating

All indexed content must comply with the [VIVERSE Terms of Use](https://www.viverse.com/terms-of-use). This covers your content itself, its title, description, and thumbnails, and any ads, links, or player-generated content it shows.

VIVERSE doesn't index content that includes:

* Nudity or sexual content.
* Gratuitous violence or gore.
* Political invective.
* Hateful, harassing, or extremist content.
* Content that sexualizes or endangers minors.
* Fraud, impersonation, phishing, or misleading claims.
* Material you don't have the rights to use, including other creators' assets, characters, music, or trademarks.

### Age rating

Set an accurate age rating in **Audience & Age restriction** when you publish in VIVERSE Studio. The rating must reflect the most mature content a player can reach, including player-generated content and external content shown inside your experience. VIVERSE may change the rating or reject the submission if the rating is inaccurate.

### Player-generated content

If players can create, upload, or share content, or communicate with each other, your content must include ways to report and block other players, rules for player behavior, and a process for handling reports.

## What you're responsible for

Some requirements can't be fully tested during review, but you're responsible for them when you submit. VIVERSE may ask you for evidence at any time.

* **Rights:** You own or have licenses for all code, art, audio, music, 3D assets, trademarks, and likenesses in your content.
* **Privacy:** You disclose the personal data, tracking, and third-party SDKs your content uses, collect only what your features need, and provide a privacy notice where required.
* **Permissions:** You request camera, microphone, location, and other sensitive permissions only when a feature needs them, and you explain why.
* **Monetization:** You disclose ads, purchases, and subscriptions, and they don't use deceptive design.

## Before you submit

Check each item below before you select **Submit for Review**. Testing in staging on your declared devices catches most issues before VIVERSE does.

* Your content averages at least 24 FPS on the minimum-spec device for each device type you declare.
* There are no crashes, endless loading screens, or blocked core interactions on any declared device.
* Your content, title, description, and thumbnails follow the content policy.
* Your age rating is accurate.
* All assets are yours or properly licensed.
* You selected the [iframe permissions](README.md#iframe-permissions) your content needs, such as camera or pointer lock.
* If your content requires sign-in, you provided test credentials and instructions.
