---
description: Use `XrService` to interact with virtual reality devices and controllers
---

# Custom Virtual Reality UX

WebXR experiences can run on desktop, mobile and virtual reality devices alike. Utilizing the `XrService` in the Create SDK, you can write custom code to manage VR controllers, locomotion settings, and XR session callbacks.

### Custom Locomotion Settings

By default, the VIVERSE character controller uses teleport for locomotion, since this is the more comfortable option, in general. However, smooth locomotion (where the player glides smoothly across the floor) can be enabled on either or both controllers by setting the [`LocomotionType`](https://viveportsoftware.github.io/pc-lib/enums/XrTypes.LocomotionTypes.html) of each.

Import the [`XrService`](https://viveportsoftware.github.io/pc-lib/classes/XrService.html) from the Create SDK into an .mjs script. Per the API docs, this gives you access to both [left and right controllers](https://viveportsoftware.github.io/pc-lib/classes/XrService.html#controllers), and their properties, to set on init:

```javascript
import { Script, Asset } from "playcanvas";
import { XrService } from "../@viverse/create-sdk.mjs";

export class ViverseXrManager extends Script {
  static scriptName = "viverseXrManager";
  
  initialize() {
    this.xrService = new XrService();
    this.xrService.controllers.right.locomotionType = 1;  // Teleport
    this.xrService.controllers.left.locomotionType = 2;  // Smooth
    // See enum definitions here:
    // https://viveportsoftware.github.io/pc-lib/enums/XrTypes.LocomotionTypes.html
  }
}
```

> **NOTE:** _this script asset must be placed in `/scripts` or another subfolder, since it assumes the VIVERSE SDK is one level up, located at: `"../@viverse/create-sdk.mjs"` - or you can alter this import path as needed. For more information on how .mjs scripts and imports work, see_ [_Introduction to MJS_](introduction-to-mjs.md)_._

When switching to Smooth Locomotion, this also enables flight in VR in any World where flight is enabled in World Settings. Just "click" the Smooth Locomotion joystick to enter flight mode. Once flying, pressing forward on the joystick will fly forward along the VR camera's forward axis (i.e. wherever you're looking), and vice versa backwards.

### Custom Controller Models

Checking the `IXrController` interface further, we can [use the `setModelAsset()` function to set custom 3D models for our controllers](https://viveportsoftware.github.io/pc-lib/interfaces/IXrController.html#setModelAsset.setModelAsset-1), instead of the default VIVERSE models.

```javascript
import { Script, Asset } from "playcanvas";
import { XrService } from "../@viverse/create-sdk.mjs";

export class ViverseXrManager extends Script {
  static scriptName = "viverseXrManager";
  
  /**
  * @attribute
  * @type {Asset}
  */
  vrControllerAssetR = null;

  /**
  * @attribute
  * @type {Asset}
  */
  vrControllerAssetL = null;

  initialize() {
    this.xrService = new XrService();
    this.xrService.controllers.right.setModelAsset(this.vrControllerAssetR);
    this.xrService.controllers.left.setModelAsset(this.vrControllerAssetL);
  }
}
```

After defining the `vrControllerAssetL` and `vrControllerAssetR` attributes of type `Asset` in the above script, we then reference custom 3D controller assets in the editor, which our script instantiates at runtime in VR.

<figure><img src="../../.gitbook/assets/image (744).png" alt=""><figcaption></figcaption></figure>

