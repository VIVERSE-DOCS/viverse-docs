---
description: >-
  The page introduces the basic information about implementing custom code with
  PlayCanvas in VIVERSE.
---

# Custom Code

***

### Fundamentals

VIVERSE allows developers to use nearly any of the custom scripting interfaces [provided by PlayCanvas](https://developer.playcanvas.com/user-manual/scripting/), including but not limited to WebRequests like http & websockets, GLSL shaders, and 3rd party libraries imported directly into your project's hierarchy.&#x20;

However, by default, VIVERSE's PlayCanvas SDK does automatically handles avatar transform and audio networking. For customizing any features related to the VIVERSE avatar, we provide an additional API reference (see VIVERSE SDK APIs below).

### VIVERSE SDK APIs

The documentation for VIVERSE's SDK APIs is currently housed here: [https://viveportsoftware.github.io/pc-lib/](https://viveportsoftware.github.io/pc-lib/). We provide interfaces for customizing the default VIVERSE Cameras, Player, Network, and Environment, as well as the unique attributes when in an XR/immersive session in a VR headset.

These interfaces are automatically injected when you activate the VIVERSE Chrome Extension; The folder @viverse is added to your asset folder, which contains all interfaces in the create-sdk.mjs file. All interfaces can be imported and manipulated in your custom scripts.

{% hint style="warning" %}
For accessing the APIs in the VIVERSE SDK, you must use .mjs file types, which support import and export statements.
{% endhint %}

### A Note on No-Code Tools

In addition to adding the create-sdk.mjs file on initialization, the VIVERSE Chrome Extension adds create-extensions-sdk.mjs, which contains all of the interfaces between the no-code tools and PlayCanvas.

Within this file, you can see that each no-code function is assigned a unique trigger string, such as 'trigger:300006' for EntitySubscribeTriggerEnter. This is useful, because you can arbitrarily listen for and fire these exact same event names in custom scripts, connecting together the VIVERSE No-Code Functions and your custom code.

