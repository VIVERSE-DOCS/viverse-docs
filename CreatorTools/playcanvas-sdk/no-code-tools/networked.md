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
