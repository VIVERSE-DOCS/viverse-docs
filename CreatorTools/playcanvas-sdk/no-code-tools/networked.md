---
description: >-
  This page details the usage of the networked component on individual entities
  in PlayCanvas.
---

# Networked

***

## Overview

The Networked plugin allows local updates to certain properties of an entity's components to be sent to other connected avatars.

<figure><img src="../../.gitbook/assets/Screenshot 2025-02-10 at 8.19.59 PM.png" alt="" width="375"><figcaption></figcaption></figure>

### Transform

By adding the Transform component, an entity's position and rotation will be networked across clients.

{% hint style="warning" %}
At this time, Transform does not network the Scale property of the entity.
{% endhint %}

### Anim

By adding the Anim component, an entity's animation state will be networked across clients.



## Networking Example

In this video, we have created an arena with a floor, four walls and a ball. The networking module has been added to the ball. After publishing and creating the world in VIVERSE, multiple players can join in the environment. The location of the ball is tracked across all player sessions.

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F4pMiThqqrBzfvP8uy5am%2Fuploads%2FxbEue0WzcXozheoJtWwi%2F2025-04-11%2013-07-02%20(online-video-cutter.com).mp4?alt=media&token=b0d3e416-7d21-4694-8610-a2b01b3951df" %}

To create the arena and ball in the video, you can follow the [Create Your First PlayCanvas Project](../tutorials/create-your-first-playcanvas-project.md) tutorial. The instructions below can be used to add Networking functionality to the ball or any other entity of your choosing.

{% stepper %}
{% step %}
### Create the entity

A. We have already created the **Ball** entity in the tutorial linked above. Here's a screenshot of the  **Ball** entity.

<figure><img src="../../.gitbook/assets/image (652).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the Networking module

A. In the VIVERSE extension, select the **Networking** plugin for the **Select plugins** dropdown.

B. Change the dropdown to **Transform** in the **Select a module and add** field. Click the plus sign.

C. Confirm the **enabled** checkbox is checked.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Publish and create world

A. To test the project, publish to VIVERSE and create the world.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Share and play

A. Share the world link with another player and have them join the environment. We the other player interacts with the ball, you should be able to see the ball movement in your session. When you move the ball, the other player should be able to see the ball movement in their session.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
