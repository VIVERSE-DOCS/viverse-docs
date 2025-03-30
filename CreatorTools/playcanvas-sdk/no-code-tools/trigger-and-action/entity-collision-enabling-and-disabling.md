---
description: >-
  This document provides several guides that can be used to enable and disable
  colliders in a VIVERSE project. These actions can be configured to execute
  when triggers are activated.
---

# Entity Collision Enabling & Disabling

***

## EntityToggleCollision

**An Action That Toggles An Object's Collider On/Off**

This guide provides instructions for setting up the **EntityToggleCollision** action. In the sample app, every time the avatar enters the green trigger area, a notification is sent to the wall and the collider on the wall is toggled on/off. The wall is green when the avatar can pass through and red when the avatar can not pass through.

| <img src="../../../.gitbook/assets/image (469).png" alt="" data-size="original">                         | <img src="../../../.gitbook/assets/image (470).png" alt="" data-size="original">                                                                         |
| -------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Before the avatar enters the green trigger area, the wall is red and the avatar can not pass through it. | Once the avatar enters the green trigger area, the collider on the wall is toggled to be disabled. The wall is green and the avatar can pass through it. |

In this example, a trigger is created and when the avatar enters the trigger area, an action sends a notification to toggle another object's collider on/off. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.&#x20;

{% stepper %}
{% step %}
### Create the 3D object that will send the notification

A. Add a 3D object to the scene.

B. The **Collision** component is not required for **EntityToggleCollision** action to work. The **Collision** component is required for the **EntitySubscribeTriggerEnter** trigger that will be used in this example.

C. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (471).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the NotificationCenterPublish action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **NotificationCenterPublish**

E. Create a unique notification name and add it to the **notification name to publish** field. In this example, the notification is called **ToggleWall**.

<figure><img src="../../../.gitbook/assets/image (472).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the 3D object that will receive the notification

A. Add a 3D object to the scene.

B. Add a **Collision** component.

C. Add a **Rigidbody** component.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (473).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityToggleCollision action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **NotificationCenterSubscribe**.

C. The same text that was added to the **notification name to publish** needs to be added to the **notification name to subscribe**.

D. Add an **Action** and select **EntityToggleCollision.**

<figure><img src="../../../.gitbook/assets/image (477).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntityEnableCollision

**Create An Action That Enables An Object's Collider**

This guide provides instructions for setting up the **EntityEnableCollision** action. In the sample app, every time the avatar enters the red trigger area, a notification is sent to the wall object and the collider on the wall object is enabled. The wall is green when the avatar can pass through and red when the avatar can not pass through.

| <img src="../../../.gitbook/assets/image (474).png" alt="" data-size="original">  | <img src="../../../.gitbook/assets/image (475).png" alt="" data-size="original">                          | <img src="../../../.gitbook/assets/image (476).png" alt="" data-size="original">   |
| --------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| The avatar is able to pass through the wall when the wall’s collider is disabled. | Once the avatar enters the red trigger area, the EntityEnableCollision action enables the wall’s collider | The avatar is unable to pass through the wall once the wall’s collider is enabled. |

In this example, a trigger is created and when the avatar enters the trigger area, an action sends a notification to enable another object's collider. Any object can be used as a trigger, as long as the object has a collision component.&#x20;

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. Add a **Collision** component.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (481).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the **EntitySubscribeTriggerEnter** trigger

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

E. Add an **Action** and select **NotificationCenterPublish.**

F. Create a unique notification name and add it to the **notification name to publish** field. In this example, the notification is called **EnableWall**.

<figure><img src="../../../.gitbook/assets/image (482).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the object that will have it's collider enabled

A. Create a new **3D Box** entity.

B. Add a **Collision** component.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (483).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityEnableCollision action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **NotificationCenterSusbscribe**.

C. The same text that was added to the **notification name to publish** needs to be added to the **notification name to subscribe**.

D. Add an **Action** and select **EntityEnableCollision.**

<figure><img src="../../../.gitbook/assets/image (484).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}





## EntityDisableCollision

**Create An Action That Disables An Object's Collider**

This guide provides instructions for setting up the **EntityDisableCollision** action. In the sample app, every time the avatar enters the green trigger area, a notification is sent to the wall object and the collider on the wall object is disabled. The wall is green when the avatar can pass through and red when the avatar can not pass through.

| <img src="../../../.gitbook/assets/image (485).png" alt="" data-size="original">   | <img src="../../../.gitbook/assets/image (486).png" alt="" data-size="original">       | <img src="../../../.gitbook/assets/image (487).png" alt="" data-size="original">  |
| ---------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| The avatar is unable to pass through the wall when the wall’s collider is enabled. | Once the avatar enters the green trigger area, the action disables the wall’s collider | The avatar is able to pass through the wall when the wall’s collider is disabled. |

In this example, a trigger is created and when the avatar enters the trigger area, an action sends a notification to disable another object's collider. Any object can be used as a trigger, as long as the object has a collision component.&#x20;

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. Add a **Collision** component.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (62).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntitySubscribeTriggerEnter trigger

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

E. Add an **Action** and select **NotificationCenterPublish.**

F. Create a unique notification name and add it to the **notification name to publish** field. In this example, the notification is called **DisableWall**.

<figure><img src="../../../.gitbook/assets/image (63).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the object that will have it's collider disabled

A. Create a new **3D Box** entity.

B. Add a **Collision** component.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (64).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityDisableCollision action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **NotificationCenterSubscribe**.

C. The same text that was added to the **notification name to publish** needs to be added to the **notification name to subscribe**.

D. Add an **Action** and select **EntityDisableCollision.**

<figure><img src="../../../.gitbook/assets/image (65).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
