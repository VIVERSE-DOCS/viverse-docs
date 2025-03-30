---
description: >-
  This document provides a guide that can be used to setup Polygon Streaming in
  a VIVERSE project.
---

# Polygon Streaming

***

## Media Polygon Streaming

**Streaming 3D Models into The Environment**

{% hint style="info" %}
For Polygon Streaming using the PlayCanvas Extension, you must use the Polygon Streaming Asset ID url, not the link url.
{% endhint %}



| <img src="../../../.gitbook/assets/image (32).png" alt="" data-size="original">      | <img src="../../../.gitbook/assets/image (33).png" alt="" data-size="original"> |
| ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| The 3D models are visible in the PlayCanvas editor when utilizing Polygon Streaming. | The 3D model streaming into the environment with the avatar.                    |



{% stepper %}
{% step %}
### Create the entity that will stream in the 3D model

A. Create a new entity.

B. Change the size of the model using the **Scale**.

C. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (34).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
## Add the PolygonStreaming module

A. In the VIVERSE extension, select the **Media** plugin for the **Select plugins** dropdown.

B. Select the **PolygonStreaming** module and add it.

C. Add the **polygon streaming url.** The URL should be in the following format: **https://stream-stage.viverse.com/polygon\_file/b9e62012-11e5-49cb-8ede-de596eec537e/aee7496b-b459-4e5d-87fe-2658955eb4f0/model.xrg**

<figure><img src="../../../.gitbook/assets/image (35).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
