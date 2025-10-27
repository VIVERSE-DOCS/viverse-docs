---
description: >-
  Control how users see and express themselves with the .changeAvatar() method
  on LocalPlayer
---

# Change Avatars Programatically

If you don't want to use default VIVERSE avatars for your world, the PlayCanvas SDK features a simple method to swap to any .vrm avatar asset in your project.

[Per the API docs](https://viveportsoftware.github.io/pc-lib/index.html), import the [`PlayerService`](https://viveportsoftware.github.io/pc-lib/interfaces/IPlayerService.html) into your .mjs script, which has a [`localPlayer` property](https://viveportsoftware.github.io/pc-lib/interfaces/ILocalPlayer.html) of type `LocalPlayer`, which in turn has a [`.changeAvatar()` method](https://viveportsoftware.github.io/pc-lib/interfaces/ILocalPlayer.html#changeAvatar.changeAvatar-1).

```javascript
import { Script, Asset } from "playcanvas";
import { PlayerService } from "../@viverse/create-sdk.mjs";

export class VvSwitchAvatars extends Script {
  static scriptName = "vvSwitchAvatars";

  /**
   * @attribute
   * @type {Asset}
   */
  vrmAsset = null;

  initialize() {
    this.playerService = new PlayerService();
    this.playerService.localPlayer.changeAvatar(this.vrmAsset);
  }
}

```

> **NOTE:** _this script asset must be placed in `/scripts` or another subfolder, since it assumes the VIVERSE SDK is one level up, located at: `"../@viverse/create-sdk.mjs"` - or you can alter this import path as needed. For more information on how .mjs scripts and imports work, see_ [_Introduction to MJS_](introduction-to-mjs.md)_._

Because `vrmAsset` is defined as an attribute of type `Asset`, we can then select which asset to use directly in the PlayCanvas editor once the script is added to an entity.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

When we publish to VIVERSE and load the experience, the .vrm is loaded immediately. Avatar switching for the local player is possible at any point during runtime and can be triggered with UI, trigger colliders, or with any other programmatic callback.\
\
**Reference Code:**\
[PlayCanvas minimal reproduction project](https://playcanvas.com/project/1350550/overview/changeavatar-demo)\
[Live demo](https://create.viverse.com/DApMQ7h)

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>
