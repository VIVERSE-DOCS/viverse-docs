---
description: >-
  How to use VIVERSE SDK's CameraService to manage camera settings and switch
  between different VIVERSE and custom cameras.
---

# Camera Management: Settings and Switching

***

VIVERSE provides a powerful camera system alongside its player controller and input scripting that allows developers to switch between different views and control various camera behaviors. This guide will show you how to implement camera switching in your VIVERSE project.

### Basic Camera Switching

The VIVERSE player rig creates several cameras and switches between them depending on your settings and/or world scripting: `CAMERA_3RD`, `CAMERA_1ST`, `CAMERA_VR`, `CAMERA_ORBITAL` are all separate in the scene hierarchy.

The [`CameraService`](https://viveportsoftware.github.io/pc-lib/classes/CameraService.html) provides methods to switch between different camera views provided by the VIVERSE avatar rig. Here's a basic .mjs script importing the `CameraService`  and switching to first-person camera mode on init. Simply attach this script to any entity in your project.&#x20;

```javascript
import { Script } from "playcanvas";
import { CameraService, CameraTypes } from "../@viverse/create-sdk.mjs";

export class CameraManager extends Script {
  initialize() {
    this.cameraService = new CameraService();

    // Switch to first person view to first person 
    this.cameraService.switchPov(CameraTypes.PovTypes.FirstPerson);
    // At any time, you can switch back to CameraTypes.PovTypes.ThirdPerson

    // Prevent users from switching POV, which is usually done in user settings or with the keyboard shortcut "V"
    this.cameraService.canSwitchPov = false;
  }
}
```

> **NOTE:** _this script asset must be placed in `/scripts` or another subfolder, since it assumes the VIVERSE SDK is one level up, located at: `"../@viverse/create-sdk.mjs"` - or you can alter this import path as needed._

### Custom Camera Override Example

If the VIVERSE player rig cameras don't totally meet your needs, you can switch to your own camera entity for cut scenes or preview cameras. Here's a practical example of how to override the default VIVERSE camera with a custom camera on init:

```javascript
import { Script, Entity } from "playcanvas";
import { CameraService } from "../@viverse/create-sdk.mjs";

export class OverrideViverseCamera extends Script {
  /**
   * @attribute
   * @type {Entity}
   */
  overrideCamera = null;
  
  initialize() {
    if (!this.overrideCamera) {
      console.error("No override camera set!");
      return;
    }

    this.cameraService = new CameraService();
    this.cameraService.switchCamera(this.overrideCamera);
  }
}
```

### Camera Switching on Events: VR and Player Ready

You can listen for certain events from other services that may be related to your desired camera behavior. For example, on the `player:ready` event, we know that all player cameras and scripting have loaded in and are ready to interact with the scene:

```javascript
// Wait for the XR start event then get the VR camera
this.app.xr.on("start", () => {
  // The VIVERSE player rig will switch to its "CAMERA_VR" automatically
  // So getting reference to the activeCamera at this time will point to
  // the "CAMERA_VR" entity
  const vrCamera = this.cameraService.activeCamera;
  
  // You could also search for this by name
  // const vrCamera = this.app.root.findByName("CAMERA_VR");
  
  // Then raycast from the forward vector, apply scripts, etc.
});

// Or wait for player to be ready before switching cameras
this.playerService.localPlayer.on("player:ready", () => {
  this.cameraService.switchCamera(this.previewCamera);
});
```

### Additional Camera Properties

The `CameraService` provides several properties to customize camera behavior:

```javascript
// Set minimum and maximum zoom distances
this.cameraService.minZoomDistance = 1;
this.cameraService.maxZoomDistance = 10;

// Disable camera zooming
this.cameraService.canZoom = false;

// Disable camera rotation
this.cameraService.canRotate = false;

// Get current POV type
const currentPov = this.cameraService.pov; // Returns PovTypes enum value

// Get active camera entity
const activeCamera = this.cameraService.activeCamera; // Returns null | Entity
```

### Additional Resources

* [VIVERSE SDK API Documentation](https://viveportsoftware.github.io/pc-lib/)
* [PlayCanvas Scripting Guide](https://developer.playcanvas.com/user-manual/scripting/)
