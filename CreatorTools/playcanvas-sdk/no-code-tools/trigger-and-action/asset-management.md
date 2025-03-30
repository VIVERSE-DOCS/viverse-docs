---
description: >-
  This document provides several guides that can be used to optimize VIVERSE
  projects by controlling when assets are rendered.
---

# Asset Management

***

## EntityAssetUnload

**Create An Action That Unloads An Asset From The Scene, Stops The Asset From Rendering And Retains The Asset In Memory**

This guide provides instructions for setting up the **EntityAssetUnload** action. In the sample app, the trigger area is outlined in blue. Once the avatar enters into the trigger area, the asset stops rendering in the scene. This will decrease draw calls and improve frames per second, but the asset will be retained in memory.&#x20;



| <img src="../../../.gitbook/assets/image (531).png" alt="" data-size="original">     | <img src="../../../.gitbook/assets/image (532).png" alt="" data-size="original">                      |
| ------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------- |
| When the avatar is outside the blue trigger area, the house asset is being rendered. | When the avatar enters the blue trigger area, the house asset is unloaded, but is retained in memory. |

In this example, a trigger is created and when triggered, an action unloads an object. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. The **Collision** component is not required for **EntityAssetUnload** action to work. The **Collision** component is required for the **EntitySubscribeTriggerEnter** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (533).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityAssetUnload action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityAssetUnload.**

E. Add an object that will be unloaded when the avatar enters the trigger area. The **House2** entity has been added to the **pick up specify execution entity**.

<figure><img src="../../../.gitbook/assets/image (534).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}







## EntityAssetReload

**Create An Action That Reloads An Asset Into The Scene From Memory And Causes The Asset To Be Rendered**

This guide provides instructions for setting up the **EntityAssetReload** action. In the sample app, the trigger area is outlined in blue. Once the avatar leaves the trigger area, the asset is reloaded from memory and starts rendering in the scene.

| <img src="../../../.gitbook/assets/image (529).png" alt="" data-size="original">                      | <img src="../../../.gitbook/assets/image (530).png" alt="" data-size="original">       |
| ----------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| When the avatar enters the blue trigger area, the house asset is unloaded, but is retained in memory. | When the avatar leaves the blue trigger area, the house asset is reloaded from memory. |

In this example, a trigger is created and when triggered, an action reloads an object. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. The **Collision** component is not required for **EntityAssetReload** action to work. The **Collision** component is required for the **EntitySubscribeTriggerLeave** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (535).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityAssetReload action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerLeave**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityAssetReload.**

E. Add an object that will be unloaded when the avatar enters the trigger area. The **House2** entity has been added to the **pick up specify execution entity**.

<figure><img src="../../../.gitbook/assets/image (536).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}







## EntityDestroy

**Create An Action That Destroys An Asset, Removes It From Memory and Stops It From Rendering**

This guide provides instructions for setting up the **EntityDestroy** action. In the sample app, the trigger area is outlined in blue. Once the avatar enters the trigger area, the asset is destroyed, which removes it from memory and stops it from rendering.

| <img src="../../../.gitbook/assets/image (537).png" alt="" data-size="original">     | <img src="../../../.gitbook/assets/image (538).png" alt="" data-size="original">                    |
| ------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------- |
| When the avatar is outside the blue trigger area, the house asset is being rendered. | When the avatar enters the blue trigger area, the house asset is destroyed and removed from memory. |

In this example, a trigger is created and when triggered, an action destroys an object. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. The **Collision** component is not required for **EntityDestroy** action to work. The **Collision** component is required for the **EntitySubscribeTriggerEnter** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (539).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityDestroy action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityDestroy.**

E. Add an object that will be destroyed when the avatar enters the trigger area. The **House2** entity has been added to the **pick up specify execution entity**.

<figure><img src="../../../.gitbook/assets/image (540).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
