---
description: >-
  This document provides guides that can be used to change the spawn location of
  an avatar and transfer an avatar to a different location. These actions can be
  configured to execute with triggers.
---

# Avatar Teleport & Checkpoint

***

## EntityCheckPoint

**Create An Action That Sets A New Spawn Point**

This guide provides instructions for setting up the **EntityCheckPoint** action. In the sample app, once the avatar enters one of the green trigger areas, the area becomes the new spawn location.

| <img src="../../../.gitbook/assets/image (489).png" alt="" data-size="original">              | <img src="../../../.gitbook/assets/image (488).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (490).png" alt="" data-size="original"> |
| --------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| The avatar enters the green trigger area and the trigger area becomes the new spawn location. | The avatar has fallen off the map and needs to be respawned.                     | When the avatar respawns, it is spawned at the new location.                     |

In this example, a trigger area is created and when triggered, an action sets a new spawn location. Any object can be used as a trigger, as long as the object has a collision component.&#x20;

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **Sphere** entity.

B. The **Collision** component is not required for **EntityCheckPoint** action to work. The **Collision** component is required for the **EntitySubscribeTriggerEnter** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (491).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityCheckPoint action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityCheckPoint.**

E. Add an entity that has a position that will be used for the new spawn location. **SpawnLocation1** has been added to the **pick up an entity id**.

<figure><img src="../../../.gitbook/assets/image (492).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## TeleportAvatar

**Create An Action That Teleports An Object To A Specific Location**

This guide provides instructions for setting up the **TeleportAvatar** action. In the sample app, once the avatar enters one of the green cylinder trigger areas, the avatar is teleported to another location.

| <img src="../../../.gitbook/assets/image (496).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (497).png" alt="" data-size="original">     |
| -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| The avatar enters the green trigger area.                                        | After entering the green trigger area, the avatar is teleported to another location. |

In this example, a trigger is created and when the avatar or other objects enter the trigger area, an action teleports the avatar to a specific location. Any object can be used as a trigger, as long as the object has a collision component.&#x20;

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **Sphere** entity.

B. The **Collision** component is not required for **TeleportAvatar** action to work. The **Collision** component is required for the **EntitySubscribeTriggerEnter** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (498).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the TeleportAvatar action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityTeleportAvatar.**

E. Add an entity that has a position that will be used for the teleport location. **TeleportLocation2** has been added to the **Specify the Entity whose location you want to teleport to**.

<figure><img src="../../../.gitbook/assets/image (499).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
