---
description: >-
  This document provides several guides that can be used to setup event
  listeners in a VIVERSE project. The event listeners use triggers. Triggers can
  be configured to perform actions once activated.
---

# Event Listeners

***

## EntitySubscribeTriggerEnter

**Create A Trigger Based On When An Object Enters An Area**

This guide provides instructions for setting up the **EntitySubscribeTriggerEnter** trigger. In the sample app, the trigger areas are outlined in blue. Once the avatar enters into the blue area, an action occurs.

| <img src="../../../.gitbook/assets/image (400).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (401).png" alt="" data-size="original"> |
| -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| The avatar is outside the blue trigger area and the gold coin is not visible.    | The avatar has entered the blue trigger area and the gold coin is visible.       |

In this example, a trigger area is created and when the avatar enters the trigger area, an action is initiated. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. Add a **Collision** component.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (480).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the **EntitySubscribeTriggerEnter** trigger

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityEnableById.**

E. Add an object that will be enabled when the avatar enters the trigger area. The **golden\_coin** entity has been added to the **pick up specify execution entity**. The **golden\_coin** entity has also been disabled by default.

<figure><img src="../../../.gitbook/assets/image (399).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntitySubscribeTriggerLeave

**Create A Trigger Based On When An Object Leaves An Area**

This guide provides instructions for setting up the **EntitySubscribeTriggerLeave** trigger. In the sample app, the trigger areas are outlined in blue. Once the avatar leaves the blue area, an action occurs.

| <img src="../../../.gitbook/assets/image (402).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (403).png" alt="" data-size="original"> |
| -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| The avatar is inside the the blue trigger area and the gold coin is visible.     | The avatar leaves the blue trigger area and the gold coin is no longer visible.  |

In this example, a trigger area is created and when the avatar leaves the trigger area, an action is initiated. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. Add a **Collision** component.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

<figure><img src="../../../.gitbook/assets/image (479).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the **EntitySubscribeTriggerLeave** trigger

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerLeave**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityDisableById.**

E. Add an object that will be disabled when the avatar leaves the trigger area. The **golden\_coin** entity has been added to the **pick up specify execution entity**.

<figure><img src="../../../.gitbook/assets/image (405).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntitySubscribeCollisionStart

**Create A Trigger Based On When An Object Collides With Another Object**

This guide provides instructions for setting up the **EntitySubscribeCollisionStart** trigger. In the sample app, the **golden\_coin** is a trigger. Once the avatar collides with the **golden\_coin**, an action occurs.

| <img src="../../../.gitbook/assets/image (408).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (407).png" alt="" data-size="original"> |
| -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| The avatar approaches the gold coin.                                             | Once the avatar collides with the gold coin, the gold coin is removed.           |

In this example, a trigger is created and when the avatar collides with the trigger, an action is initiated. Any object can be used as a trigger, as long as the object has a collision component. Because this specific example uses a 3D object that the avatar can collide with, a **RigidBody** component needs to be added. In this example, the object **golden\_coin** is used, but a simple cube will suffice.

{% stepper %}
{% step %}
### Create the 3D object that the avatar will collide with

A. Add 3D object to the scene.

B. Add a **Collision** component.

C. Add a **Rigidbody** component.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (409).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntitySubscribeCollisionStart trigger

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeCollisionStart**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityDisable.**

<figure><img src="../../../.gitbook/assets/image (410).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}



## EntitySubscribeCollisionEnd&#x20;

**Create A Trigger Based On When An Object Stops Colliding With Another Object**

This guide provides instructions for setting up the **EntitySubscribeCollisionEnd** trigger. In the sample app, a flattened 3D cube is a trigger. Once the avatar stops colliding with the cube, an action occurs.

| <img src="../../../.gitbook/assets/image (411).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (412).png" alt="" data-size="original">            |
| -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| The avatar is colliding with the red platform.                                   | Once the avatar stops colliding with the red platform, the red platform begins to fade out. |

In this example, a trigger is created and when the avatar or other objects collide with the trigger, an action is initiated. Any object can be used as a trigger, as long as the object has a collision component. Because this specific example uses a 3D object that the avatar can collide with, a **RigidBody** component needs to be added.

{% stepper %}
{% step %}
### Create the 3D object that the avatar will collide with

A. Add 3D object to the scene.

B. Add a **Collision** component.

C. Add a **Rigidbody** component.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (413).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntitySubscribeCollisionEnd trigger

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeCollisionEnd**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **EntityFadeOut.**

<figure><img src="../../../.gitbook/assets/image (414).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}



## NotificationCenterSubscribeEntityPicking

**Create A Trigger Based On When A User Clicks An Object**

This guide provides instructions for setting up the **NotificationCenterSubscribeEntityPicking** trigger. In the sample app, the user clicks on the buttons to show and hide the whale.

| <img src="../../../.gitbook/assets/image (415).png" alt="" data-size="original"> | <img src="../../../.gitbook/assets/image (416).png" alt="" data-size="original"> |
| -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| The whale is visible.                                                            | When the user clicks on the whale, the whale is disabled.                        |

In this example, a trigger area is created on a button. When the user clicks on the trigger area, an action causes the 3D model to be disabled. Any object can be used as a trigger, as long as the object has a collision component. &#x20;

{% stepper %}
{% step %}
### Create the 3D object that the user will click on

A. Add a 3D object to the scene. In this example, a button is used, but any 3D object will work.

B. Add a **Collision** component.

C. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (417).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the NotificationCenterSubscribeEntityPicking trigger&#x20;

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **NotificationCenterSubscribeEntityPicking**.

C. Add an **Action** and select **EntityDisable.** Add an object that will be disabled.

<figure><img src="../../../.gitbook/assets/image (418).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## NotificationCenterPublish & NotificationCenterSubscribe

**An Action And Trigger Combination That Allows An Object To Send A Notification And Another Object Receives The Notification**

This guide provides instructions for setting up the **NotificationCenterPublish** action and the **NotificationCenterSubscribe** trigger. In the sample app, every time the avatar enters the green trigger area, a notification is sent to the wall and the collider on the wall is toggled on/off. The wall is green when the avatar can pass through and red when the avatar can not pass through.

| <img src="../../../.gitbook/assets/image (419).png" alt="" data-size="original">                               | <img src="../../../.gitbook/assets/image (420).png" alt="" data-size="original">                                                             |
| -------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Before the avatar enters the green trigger area, the wall is red and the avatar can not pass through the wall. | Once the avatar enters the green trigger area, the green trigger sends a notification to the wall and the collider on the wall is disabled.  |

In this example, a trigger is created and when the avatar enters the trigger area, an action sends a notification to another object. The other object receives the notification via trigger, then initiates an action.&#x20;

{% stepper %}
{% step %}
### Create the 3D object that will send the notification

A. Add a 3D object to the scene.

B. The **Collision** component is not required for **NotificationCenterPublish** action or **NotificationCenterSubscribe** trigger to work. The **Collision** component is required for the **EntitySubscribeTriggerEnter** trigger that will be used in this example.

C. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (423).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the NotificationCenterPublish action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **NotificationCenterPublish.**

E. Create a unique notification name and add it to the **notification name to publish** field. In this example, the notification is called **ToggleWall**.

<figure><img src="../../../.gitbook/assets/image (424).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the 3D object that will receive the notification

A. Add a 3D object to the scene.

B. The **Collision** component is not required for **NotificationCenterPublish** action or **NotificationCenterSubscribe** trigger to work. The **Collision** component is required for the **EntityToggleCollision** action that will be used in this example.

C. The **Rigidbody** component is not required for the **NotificationCenterPublish** action or **NotificationCenterSubscribe** trigger to work. The **Rigidbody** component is required for the **EntityToggleCollision** action that will be used in this example.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (426).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the NotificationCenterSubscribe action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **NotificationCenterSubscribe**.

C. The same text that was added to the **notification name to publish** needs to be added to the **notification name to subscribe**.

D. Add an **Action** and select **EntityToggleCollision.**

<figure><img src="../../../.gitbook/assets/image (427).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
