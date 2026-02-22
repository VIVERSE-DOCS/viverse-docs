---
description: >-
  This document provides a guide for creating a project in PlayCanvas that can
  be published to VIVERSE.
---

# Create Your First PlayCanvas Project

***

{% hint style="info" %}
Please follow the provided designer guideline [here](/broken/pages/Oy7laS7LpHitbo51HRhg#id-3d-asset-guide) to meet performance and feasibility specifications. This guide will help ensure your scene is optimized for the VIVERSE platform.&#x20;
{% endhint %}

{% hint style="warning" %}
Make sure to disable any camera/character control entity in the scene. As it might conflict with VIVERSE avatar system.
{% endhint %}

| <img src="../../.gitbook/assets/image (651).png" alt="" data-size="original"> |
| :---------------------------------------------------------------------------: |
|  Create a game with floor and walls. Use your avatar to move the ball around. |

{% stepper %}
{% step %}
### Create a new PlayCanvas project

A. Delete the **Camera.**

<figure><img src="../../.gitbook/assets/image (635).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the floor

A. Select the **Plane** entity and rename it **Floor.**

B. To increase the size of the **Floor,** change the **Scale** to **(20, 1, 20)**.

C. To allow the players to collide with the **Floor**, add a **Collision** component.

D. To increase the size of the **Collision** component so that it covers the entire **Floor**, change **Half Extents** to **(10, .1, 10)**.

E. To prevent the players from falling through the **Floor,** add a **Rigidbody** component.

F. Click the **Import Ammo** button.

<figure><img src="../../.gitbook/assets/image (636).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the material for the floor

A. Create a new folder and name it **Materials**.

B. Inside the **Materials** folder, create a new material and name it **FloorMaterial**.

C. Expand the **Diffuse** section and click the **Color** field.

D. For the **Color**, change the values to **(97, 255, 104).**

E. Add the **FloorMaterial** to the **Material** slot on the **Floor** entity.

<figure><img src="../../.gitbook/assets/image (637).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (638).png" alt="" width="151"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (639).png" alt="" width="202"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Creating the first wall

A. Select the **Box** entity and rename it to **Wall1**.

B. To move the wall to the side, change the **Position** to **(0, 0, 10)**.

C. To increase the size of the wall, change **Scale** to **(20, 5, .1)**.

D. To allow the players to collide with the wall, add a **Collision** component.

E. To increase the size of the **Collision** component so that it covers the entire wall, change **Half Extents** to **(10, 2.5, .1)**.

F. To prevent the players from going through the wall, add a **Rigidbody** component.

<figure><img src="../../.gitbook/assets/image (640).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the material for the wall

A. Inside the **Materials** folder, create a new material and name it **WallMaterial**.

B. Expand the **Diffuse** section and click the **Color** field.

C. For the **Color**, change the values to **(108, 240, 246).**

D. Add the **WallMaterial** to the **Material** slot on the **Wall1** entity.

<figure><img src="../../.gitbook/assets/image (641).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (642).png" alt="" width="154"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (643).png" alt="" width="173"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the second wall

A. Select **Wall1** and click CTRL + D to duplicate the wall. Rename the wall to **Wall2**.

B. Move the wall to the opposite side by changing **Position** to **(0, 0, -10)**.

<figure><img src="../../.gitbook/assets/image (644).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Create the third wall

A. Select **Wall2** and click `CTRL + D` to duplicate the wall. Rename the wall to **Wall3**.

B. To place the wall in the correct space, change the **Position** to **(-10, 0, 0)**.

C. To have the wall face the correct direction, change the **Rotation** to **(0, 90, 0)**.

<figure><img src="../../.gitbook/assets/image (645).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Creating the fourth wall**

A. Select **Wall3** and click `CTRL + D` to duplicate the wall. Rename the wall to **Wall4**.

B. Move the wall to the opposite side by changing **Position** to **(10, 0, 0)**.

<figure><img src="../../.gitbook/assets/image (89).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Create the ball**

A. Add a **3D Sphere** to the scene. Rename it to **Ball**.

B. Raise the **Ball** above the ground by changing **Position** to **(0, 1, 0)**.

C. To allow the players to collide with the **Ball**, add a **Collision** component.

D. To change the collider shape to be the same shape as the **Ball,** change **Type** to **Sphere**.

E. To prevent players from passing through the **Ball**, add a **Rigidbody** component.

F. Since the **Ball** will be moving, change the **Type** to **Dynamic**.

G. Decrease the **Friction** to **.1** so that the **Ball** doesn't lose as much velocity when coming in contact with other entities.

H. Increase the **Restitution** to **1** to maximize the bounciness of the **Ball**.

<figure><img src="../../.gitbook/assets/image (646).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Create the material for the ball**

A. Inside the **Materials** folder, create a new material and name it **BallMaterial**.

B. Expand the **Diffuse** section and click the **Color** field.

C. For the **Color**, change the values to **(244, 139, 139).**

D. Add the **BallMaterial** to the **Material** slot on the **Ball** entity.

<figure><img src="../../.gitbook/assets/image (647).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (648).png" alt="" width="162"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (649).png" alt="" width="172"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### **Create a spawn point**

A. Add a new **Entity** to the scene. Rename the entity to **SpawnPoint**.

B. Add `spawn-point` to the **Tags** field.

C. To have the spawn point above the ground and close to the wall, change the **Position** to **(0, 1, 8)**.

D. To change the direction of the **SpawnPoint** so that the player will face the ball when spawned in, change **Rotation** to **(0, 180, 0)**.

<figure><img src="../../.gitbook/assets/image (650).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Publish and test

A. Publish the project to VIVERSE and test the functionality of moving in the environment and pushing the ball.
{% endstep %}
{% endstepper %}
