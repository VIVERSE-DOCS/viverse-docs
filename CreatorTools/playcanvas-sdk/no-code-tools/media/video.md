---
description: >-
  This document provides a guide that can be used to setup videos and extend the
  functionality of videos in a VIVERSE project.
---

# Video

***

## Media Video

**Adding Video And Extending Video Functionality**

| <img src="../../../.gitbook/assets/image (25).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (26).png" alt="" data-size="original"> |
| ------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| The user uses the mouse to hover over the video to access the controls.         | The video is playing.                                                           |



{% stepper %}
{% step %}
### Setup video

A. Create a new entity

B. Resize the video using the **Scale**.

C. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (27).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the video module

A. In the VIVERSE extension, select the **Media** plugin for the **Select plugins** dropdown. Select either "Asset" for files contained in your PlayCanvas project, or "URL" for a video to stream, such as a YouTube URL (but please note: YouTube or other embeds are run inside iframe elements, which do not render in VR).

B. Select the **Video** module and add it.

C. Add the video to the **Asset** field.

D. Uncheck the **auto play** property to prevent the video from starting when the avatar enters the environment.

<figure><img src="../../../.gitbook/assets/image (28).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
