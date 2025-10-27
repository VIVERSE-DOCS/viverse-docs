---
description: >-
  Learn how to navigate the differences between PlayCanvas' test servers and
  VIVERSE's production environment.
hidden: true
---

# PlayCanvas-to-VIVERSE: Development Best Practices

For rapid iteration during development, its helpful to use PlayCanvas' built-in "Launch" button, which will open your project on their test servers in just a few seconds. It's likely that many of your project's non-networked, non-avatar features can be tested and developed in isolation there.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

But in order to power its VIVERSE avatar and multiplayer features, we maintain our own version of the PlayCanvas engine and bundle your game with extra scripts and features whenever you publish with the VIVERSE [PlayCanvas Extension](playcanvas-extension-setup.md). This can result in subtle but important differences between the PlayCanvas development environment and VIVERSE's production site. This article will cover those differences and how to mitigate them.

### Networked Avatars

VIVERSE injects its own avatar, input and networking scripts on export that will by default take over any camera or player controller you set up when the project is loaded on VIVERSE. There are two ways to handle this:

1. **Go with it.** Utilize VIVERSE networked avatars and its input scripts to be the main player controller in your game. You can use the CameraService to manage third-person and first-person perspectives.
   1. **PROS:** built-in keyboard and mouse, touch and VR controls that trigger animations and emotes
   2. **CONS:** you have to publish to test most changes, since your player controller only runs on VIVERSE
2. **Disable VIVERSE avatar, cameras and input scripts** and switch to your own custom player controller scripts.
   1. **PROS:** Total control over your character controller&#x20;
   2. **CONS:** you lose the default avatar networking - any syncing between multiplayer clients must be done manually.

### VIVERSE UI

VIVERSE also injects its own custom UI into. As you design UI for your own project, be mindful that once deployed, elements may overlap, potentially blocking clicks or other input.\


<figure><img src="../.gitbook/assets/image (7) (1).png" alt="" width="375"><figcaption><p>VIVERSE default desktop UI, including optional Quests/Missions.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4) (1) (1).png" alt="" width="375"><figcaption><p>Audio settings within the "World preferences" tab of VIVERSE's UI.</p></figcaption></figure>

This UI lives under the CSS ID `#world-root`  and can be hidden with a simple `display: none` . However, in that case, be mindful that microphone, voice chat, and avatar controls will be hidden from the user, which could impact your game if you aren't deliberately turning these features off.

