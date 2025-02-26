---
description: >-
  This document provides several guides that can be used to show and hide
  objects in a VIVERSE project. These actions can be configured to execute when
  triggers are activated.
---

# Entity Enabling & Disabling

***

## EntityDisable

**Create An Action That Disables An Object**

This guide provides instructions for setting up the **EntityDisable** action. In the sample app, once the avatar collides with the gold coin, the gold coin is disabled.

| <img src="../../../.gitbook/assets/image (66).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (67).png" alt="" data-size="original"> |
| ------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| The avatar is not colliding with the gold coin.                                 | Once the avatar collides with gold coin, the gold coin is disabled.             |

In this example, a trigger is created and when triggered, an action disables an object.&#x20;

{% stepper %}
{% step %}
### Create the 3D Object that will be disabled

A. Add 3D object to the scene.

B. The **Collision** component is not required for **EntityDisable** action to work. The **Collision** component is required for the **EntitySubscribeCollisionStart** trigger that will be used in this example.

C. The **Rigidbody** component is not required for the **EntityDisable** action to work. The **Rigidbody** component is required for the **EntitySubscribeCollisionStart** trigger that will be used in this example.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (68).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityDisable action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeCollisionStart**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityDisable.**

<figure><img src="../../../.gitbook/assets/image (69).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntityEnableById

**Create An Action That Enables A Single Object**

This guide provides instructions for setting up the **EntityEnableById** action. In the sample app, once the avatar enters the trigger area, the gold coin is enabled.

| <img src="../../../.gitbook/assets/image (70).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (71).png" alt="" data-size="original"> |
| ------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| When the avatar is outside of the blue trigger area, the gold coin is disabled. | When the avatar enters the blue trigger area, the gold coin is enabled.         |

In this example, a trigger is created and when triggered, an action enables an object. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. The **Collision** component is not required for **EntityDisableById** action to work. The **Collision** component is required for the **EntitySubscribeTriggerEnter** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (463).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the **EntityEnableById** action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityEnableById.**

E. Add an object that will be enabled when the avatar enters the trigger area. The **golden\_coin** entity has been added to the **pick up specify execution entity**. The **golden\_coin** entity has also been disabled by default.

<figure><img src="../../../.gitbook/assets/image (437).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntityDisableById

**Create An Action That Disables A Single Object**

This guide provides instructions for setting up the **EntityDisableById** action. In the sample app, once the avatar leaves the trigger area, the gold coin is disabled.

| <img src="../../../.gitbook/assets/image (428).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (429).png" alt="" data-size="original"> |
| -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| When the avatar is inside the blue the trigger area, the gold coin is enabled.   | When the avatar leaves the blue trigger area, the gold coin is disabled.         |

In this example, a trigger is created and when triggered, an action disables an object. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. The **Collision** component is not required for **EntityDisableById** action to work. The **Collision** component is required for the **EntitySubscribeTriggerLeave** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

<figure><img src="../../../.gitbook/assets/image (450).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
Add the **EntityDisableById** action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerLeave**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityDisableById.**

E. Add an object that will be disabled when the avatar leaves the trigger area. The **golden\_coin** entity has been added to the **pick up specify execution entity**. The **golden\_coin** entity has also been disabled by default.

<figure><img src="../../../.gitbook/assets/image (431).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntityEnableByTag

**Create An Action That Enables Multiple Objects**

This guide provides instructions for setting up the **EntityEnableByTag** action. In the sample app, once the avatar enters the trigger area, multiple balls are enabled.

| <img src="../../../.gitbook/assets/image (433).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (434).png" alt="" data-size="original"> |
| -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| When the avatar is outside the blue trigger area, the balls are disabled.        | When the avatar enters the blue trigger area, the balls are enabled.             |

In this example, a trigger is created and when triggered, an action enables multiple objects. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. The **Collision** component is not required for **EntityEnableByTag** action to work. The **Collision** component is required for the **EntitySubscribeTriggerEnter** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (451).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the **EntityEnableByTag** action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityEnableByTag.**

E. Create a unique **Tag** and add it to the **enable entity with tag** field. In this example, the **Balls** tag is added.

<figure><img src="../../../.gitbook/assets/image (439).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add Tag to multiple objects

A. Multiple objects can be enabled using the **EntityEnableByTag** action. Create multiple objects in the scene.

B. In the **Tags** field, add the same tag from the **enable entity with tag** field. Do this for all of the objects or parents of multiple objects.

<figure><img src="../../../.gitbook/assets/image (441).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntityDisableByTag

**Create An Action That Disables Multiple Objects**

This guide provides instructions for setting up the **EntityDisableByTag** action. In the sample app, once the avatar leaves the trigger area, multiple balls are disabled.

| <img src="../../../.gitbook/assets/image (442).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (443).png" alt="" data-size="original"> |
| -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| When the avatar enters the blue trigger area, the balls are enabled.             | When the avatar leaves the blue trigger area, the balls are disabled.            |

In this example, a trigger is created and when triggered, an action disables multiple objects. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. The **Collision** component is not required for **EntityDisableByTag** action to work. The **Collision** component is required for the **EntitySubscribeTriggerLeave** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (452).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the **EntityDisableByTag** action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerLeave**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityDiableByTag.**

E. Create a unique **Tag** and add it to the **enable entity with tag** field. In this example, the **Balls** tag is added.

<figure><img src="../../../.gitbook/assets/image (445).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add Tag to multiple objects

A. Multiple objects can be enabled using the **EntityEnableByTag** action. Create multiple objects in the scene.

B. In the **Tags** field, add the same tag from the **enable entity with tag** field to all of the objects or parents of multiple objects.

<figure><img src="../../../.gitbook/assets/image (446).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntityFadeIn

**Create An Action That Fades In An Object**

This guide provides instructions for setting up the **EntityFadeIn** action. In the sample app, once the avatar starts colliding with the red platform, the red platform will fade in.

| <img src="../../../.gitbook/assets/image (447).png" alt="" data-size="original">                | <img src="../../../.gitbook/assets/image (448).png" alt="" data-size="original">                                |
| ----------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| The red platform has been faded out and is invisible, but the avatar can still collide with it. | Once the avatar begins colliding with the invisible red platform, the red platform fades in and is now visible. |

In this example, a trigger is created and when triggered, an action fades in the object it has been added to. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create the 3D object that will fade in

A. Add a 3D object to the scene.

B. A **Collision** component is not required for the **EntityFadeIn** action to work. The **Collision** component is required for the **EntitySubscribeCollisionStart** trigger that will be used in this example.

C. A **Rigidbody** component is not required for the **EntityFadeIn** action to work. The **Rigidbody** component is required for the **EntitySubscribeCollisionStart** trigger that will be used in this example.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (453).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
## Add the EntityFadeIn action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeCollisionStart**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityFadeIn**. To customize how long before the fade begins or duration of the fade, update **delay in ms** and **duration in ms** fields.

<figure><img src="../../../.gitbook/assets/image (454).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntityFadeOut

**EntityFadeOut** - Create An Action That Fades Out An Object

This guide provides instructions for setting up the **EntityFadeOut** action. In the sample app, once the avatar stops colliding with the red platform, the red platform will fade out.

| <img src="../../../.gitbook/assets/image (455).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (456).png" alt="" data-size="original">            |
| -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| The avatar is colliding with the red platform.                                   | Once the avatar stops colliding with the red platform, the red platform begins to fade out. |

In this example, a trigger is created and when triggered, an action fades an object. Any object can be used as a trigger, as long as the object has a collision component.&#x20;

{% stepper %}
{% step %}
### Create the 3D object that will fade out

A. Add a 3D object to the scene.

B. A **Collision** component is not required for the **EntityFadeOut** action to work. The **Collision** component is required for the **EntitySubscribeCollisionEnd** trigger that will be used in this example.

C. A **Rigidbody** component is not required for the **EntityFadeOut** action to work. The **Rigidbody** component is required for the **EntitySubscribeCollisionEnd** trigger that will be used in this example.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (457).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityFadeOut action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeCollisionEnd**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityFadeOut**. To customize how long before the fade begins or duration of the fade, update **delay in ms** and **duration in ms** fields.

<figure><img src="../../../.gitbook/assets/image (458).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntityToggleEnabled

**Create An Action That Toggles An Object's Enabled State**

This guide provides instructions for setting up the **EntityToggleEnabled** action. In the sample app, every time the avatar enters the blue trigger area, a notification is sent to the purple box to toggle it’s enabled state.

<table data-header-hidden data-full-width="false"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><img src="../../../.gitbook/assets/image (459).png" alt="" data-size="original"></td><td><img src="../../../.gitbook/assets/image (460).png" alt="" data-size="original"></td></tr><tr><td>The purple box is enabled/visible before the avatar enters the blue trigger area.</td><td>Once the avatar enters the blue trigger area, a notification is sent to the purple box to toggle it’s enabled stated. The purple box is disabled.</td></tr><tr><td><img src="../../../.gitbook/assets/image (461).png" alt="" data-size="original"></td><td><img src="../../../.gitbook/assets/image (462).png" alt="" data-size="original"></td></tr><tr><td>The avatar leaves the blue trigger area.</td><td>Once the avatar enters the blue trigger area again, a notification is sent to the purple box to toggle it’s enabled stated. The purple box is enabled again.</td></tr></tbody></table>

In this example, a trigger is created and when the avatar or other objects enter the trigger area, an action sends a notification to toggle another object's enabled state. Any object can be used as a trigger, as long as the object has a collision component.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. A **Collision** component is not required for the **EntityToggleEnabled** action to work. The **Collision** component is required for the **EntitySubscribeTriggerEnter** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (464).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the NotificationCenterPublish action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **NotificationCenterPublish.**

E. Create a unique name for the notification and add it to the **notification name to publish** field. In this example, the **ToggleEnabled** name is added.

<figure><img src="../../../.gitbook/assets/image (465).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create a 3D object that will have it's enabled state toggled

A. Add a 3D object to the scene.

B. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (466).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the NotificationCenterSubscribe trigger

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **NotificationCenterSubscribe**.

C. In the **notification name to subscribe** field, add the same name from the **notification name to publish** field.

D. Add an **Action** and select **EntityToggleEnabled**.

<figure><img src="../../../.gitbook/assets/image (468).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
