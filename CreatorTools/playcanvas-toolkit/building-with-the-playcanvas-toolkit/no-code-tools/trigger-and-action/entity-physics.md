---
description: >-
  This document provides a guide that can be used to add a physics force to
  objects in VIVERSE project. The action can be configured to execute when a
  trigger is activated.
---

# Entity Physics

***

## EntityRigidbodyAddForceInPhysics

**Create An Action That Adds Force To An Object**

This guide provides instructions for setting up the **EntityRigidbodyAddForceInPhysics** action. In the sample app, once the avatar enters the blue trigger area, a force is applied to the sphere.

| <img src="../../../../.gitbook/assets/image (56).png" alt="" data-size="original"> | <img src="../../../../.gitbook/assets/image (57).png" alt="" data-size="original">                                  |
| ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| The sphere is not moving before the avatar enters the trigger area.                | Once the avatar enters the trigger area, a force is applied to the sphere in the direction based on the parameters. |

In this example, a trigger is created and when the avatar or other objects enters the trigger area, an action adds force to an object. Any object can be used as a trigger, as long as the object has a collision component. This example uses a 3D box as the trigger area.

{% stepper %}
{% step %}
### Create a trigger area

A. Create a new **3D Box** entity.

B. This **Collision** component is not required for the **EntityRigidbodyAddForceInPhysics** action to work. The **Collision** component is required for the **EntitySubscribeTriggerEnter** trigger that will be used in this example.

C. Adding a material is optional. A transparent material has been added so that the trigger area is visible in play mode.

D. Click the **Edit Viverse Extension** button.

<figure><img src="../../../../.gitbook/assets/image (58).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntitySubscribeTriggerEnter trigger

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **EntitySubscribeTriggerEnter**.

C. Add **local-player** to the **tags to filter** field.

D. Add an **Action** and select **NotificationCenterPublish.**

E. Create a unique name for the notification and add it to the **notification name to publish** field. In this example, the **AddForce** name is added.

<figure><img src="../../../../.gitbook/assets/image (59).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
Create the 3D object that the physics force will be applied to

A. Create a new 3D object.

B. Add a **Collision** component.

C. Add a **Rigidbody** component.

D. Set the **Collision Type** to **Dynamic**.

E. Click the **Edit Viverse Extension** button.

<figure><img src="../../../../.gitbook/assets/image (60).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the EntityRigidbodyAddForceInPhysics action

A. In the VIVERSE extension, select the **TriggerAndAction** plugin for the **Select plugins** dropdown.

B. Add a **Trigger** and select **NotificationCenterSubscribe**.

C. The same text that was added to the **notification name to publish** needs to be added to the **notification name to subscribe**.

D. Add an **Action** and select **EntityRigidbodyAddForceInPhysics.**

E. In the **X Force**, **Y Force** and **Z Force** fields, add values for the amount of force to apply in each direction.

<figure><img src="../../../../.gitbook/assets/image (61).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
